---
title: Xamarin Profiler
description: Questa guida esamina le funzionalità principali del Xamarin Profiler. Esamina i profiler, la profilatura e quando devono essere usati e in un flusso di lavoro standard per la profilatura delle applicazioni Novell.
ms.prod: xamarin
ms.assetid: 3247fcee-6acc-470d-ab87-c1c511d67363
author: conceptdev
ms.author: crdun
ms.date: 06/03/2018
ms.openlocfilehash: 745c59ad50f0e8ad50a8ec56549d99b7b5e72228
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/06/2019
ms.locfileid: "70772469"
---
# <a name="xamarin-profiler"></a>Xamarin Profiler

_Questa guida esamina le funzionalità principali del Xamarin Profiler. Esamina i profiler, la profilatura e quando devono essere usati e in un flusso di lavoro standard per la profilatura delle applicazioni Novell._

Il successo di un'applicazione dipende dall'esperienza dell'utente finale. In qualità di sviluppatore, è possibile che siano state implementate alcune funzionalità molto straordinarie nell'app, ma se l'app è lenta o piena di arresti anomali, è probabile che l'utente lo elimini.

In passato, mono ha fornito un potente profiler della riga di comando per raccogliere informazioni sui programmi in esecuzione nel runtime di mono, denominato [mono log Profiler](https://www.mono-project.com/docs/debug+profile/profile/profiler/). Il Xamarin Profiler un'interfaccia grafica per mono log Profiler e supporta la profilatura di applicazioni Android, iOS, tvOS e Mac su Mac e applicazioni Android, iOS e tvOS su Windows.

Il Xamarin Profiler dispone di numerosi strumenti disponibili per la profilatura: allocazioni, cicli e profiler del tempo. In questa guida vengono esaminati gli strumenti misurati e il modo in cui analizzano l'applicazione e viene spiegato il significato dei dati presentati in ogni schermata.

Questa guida esamina gli scenari di profilatura comuni e introduce il profiler come strumento per analizzare e ottimizzare le applicazioni iOS e Android.

## <a name="download-and-install"></a>Scaricare e installare

> [!NOTE]
> È necessario essere un Sottoscrittore [Visual Studio Enterprise](https://visualstudio.microsoft.com/vs/compare/) per sbloccare questa funzionalità in Visual Studio Enterprise in Windows o Visual Studio per Mac in un Mac.

Il Xamarin Profiler è un'applicazione autonoma ed è integrato con Visual Studio per Mac e Visual Studio per abilitare la profilatura all'interno dell'IDE.

Scaricare il pacchetto di installazione per la piattaforma:

- [**macOS**](https://dl.xamarin.com/profiler/profiler-mac.pkg)
- [**Windows**](https://dl.xamarin.com/profiler/profiler-windows.msi)

Una volta scaricato, avviare il programma di installazione per aggiungere il Xamarin Profiler al sistema.

## <a name="profilers-and-profiling"></a>Profiler e profilatura

La profilatura è un passaggio importante e spesso trascurato nello sviluppo di applicazioni. La profilatura è un modulo di **analisi dinamica dei programmi** , che analizza il programma mentre è in esecuzione e in uso. Un profiler è uno strumento data mining che raccoglie informazioni sulla complessità temporale, l'utilizzo di metodi specifici e la memoria allocata. Un profiler consente di eseguire il drill-down approfondito e analizzare queste metriche per individuare le aree problematiche nel codice.

Quando si progetta e si sviluppa un'applicazione, è importante non ottimizzare in modo anomalo. ovvero il tempo di spesa per lo sviluppo di codice in aree a cui si accede raramente. Questa è la potenza della profilatura. Un profiler fornisce informazioni approfondite sulle parti più utilizzate della codebase e consente di individuare le aree in cui è necessario dedicare più tempo alla creazione di miglioramenti. Gli sviluppatori devono tenere in considerazione la posizione in cui la maggior parte del tempo viene impiegata nell'applicazione e la modalità di utilizzo della memoria da parte dell'applicazione.

La profilatura è utile in tutti i tipi di sviluppo, ma è particolarmente importante per lo sviluppo di applicazioni per dispositivi mobili. Il codice non ottimizzato è molto più evidente sulle piattaforme mobili rispetto ai computer desktop e il successo dell'app dipende da un codice accattivante e ottimizzato che funziona in modo efficiente.

## <a name="xamarin-profiler"></a>Xamarin Profiler

Il Xamarin Profiler fornisce agli sviluppatori un modo per profilare le applicazioni dall'interno di Visual Studio per Mac o Visual Studio. Il profiler raccoglie e visualizza le informazioni sull'app, che possono quindi essere usate dallo sviluppatore per analizzare il comportamento di un'applicazione. Esistono diversi modi per profilare un'applicazione con il Xamarin Profiler, ovvero la profilatura della memoria e il campionamento statistico. Questi vengono eseguiti rispettivamente tramite le allocazioni e gli strumenti del profiler temporale.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

Attualmente, il Xamarin Profiler può essere usato per testare le applicazioni Novell. iOS, Novell. Android e Novell. Mac in Mac (tramite Visual Studio per Mac). Il profiler è un processo separato dall'IDE, quindi, oltre a essere avviato da Visual Studio per Mac, può essere usato come applicazione autonoma per esaminare i file con estensione `.mlpd` exe e creati da [mono log Profiler](https://www.mono-project.com/docs/debug+profile/profile/profiler/).

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Attualmente, il Xamarin Profiler può essere usato per testare le app Novell. Android in Windows (tramite Visual Studio e Visual Studio per Mac). Il profiler è un processo separato dall'IDE, quindi, oltre a essere avviato da Visual Studio, può essere usato come applicazione autonoma per esaminare i file con estensione `.mlpd` exe e creati da [mono log Profiler](https://www.mono-project.com/docs/debug+profile/profile/profiler/).

-----

<a name="Profiler_Support" />

## <a name="profiler-support"></a>Supporto del profiler

Il supporto per la Xamarin Profiler è disponibile nelle piattaforme seguenti:

- Visual Studio per Mac (macOS, con licenza Enterprise)
  - Android
    - Dispositivo e emulatore
  - iOS
    - Dispositivo e simulatore
  - tvOS (lo strumento di tempo non è supportato)
    - Dispositivo e simulatore
  - Mac

- Visual Studio (solo versione **Enterprise** )
  - Android
    - Dispositivo e emulatore
  - iOS [sperimentale]
    - Dispositivo e simulatore
  - tvOS
    - Dispositivo e simulatore

Si noti che è possibile profilare **solo** le configurazioni di **debug** .

## <a name="profiler-basics"></a>Nozioni fondamentali sul profiler

Questa sezione introduce le parti del Xamarin Profiler e ne illustra le funzionalità.

### <a name="allow-profiling-in-your-app"></a>Consenti la profilatura nell'app

Prima di poter profilare correttamente l'app, è necessario consentire la profilatura nelle opzioni del progetto dell'app.

- iOS:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

  **Compilazione > debug iOS > Abilita profilatura**

  ![Finestra di dialogo Opzioni iOS in Visual Studio per Mac](images/ios-options-mac.png)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

  **Proprietà > compilazione iOS > Abilita profilatura**

  ![Finestra di dialogo Opzioni iOS in Visual Studio](images/ios-project-options-vs.png)

-----

- Android:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

  **Creare > debug Android > abilitare la strumentazione per sviluppatori**

  ![Finestra di dialogo Opzioni Android in Visual Studio per Mac](images/android-project-options.png)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

  **Creare > debug Android > abilitare la strumentazione per sviluppatori**

  ![Finestra di dialogo Opzioni Android in Visual Studio per Mac](images/android-project-options-vs-sml.png)

-----

### <a name="launching-the-profiler"></a>Avvio del profiler

Il Xamarin Profiler può essere avviato dall'IDE quando si esegue la profilatura dell'applicazione iOS o Android o come applicazione autonoma.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

#### <a name="launching-from-visual-studio-for-mac"></a>Avvio da Visual Studio per Mac

1. Assicurarsi prima di tutto che l'applicazione sia caricata in Visual Studio per Mac e selezionare la configurazione di debug (impostazione predefinita).
2. Sfogliare per **eseguire > avviare la profilatura**in Visual Studio per Mac o **analizzare > Xamarin Profiler** in Visual Studio per aprire il profiler, come illustrato nel diagramma seguente:

  ![Avvio del profiler da Visual Studio per Mac](images/start-profiling-xs.png)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

#### <a name="launching-from-visual-studio"></a>Avvio da Visual Studio

1. Prima di tutto, assicurarsi che l'applicazione sia caricata in Visual Studio e selezionare la configurazione di debug (impostazione predefinita), come specificato in precedenza.
2. Sfogliare per **analizzare > Xamarin Profiler** in Visual Studio per aprire il profiler, come illustrato nel diagramma seguente:

![Avvio del profiler da Visual Studio](images/start-profiling-vs.png)

-----

Se le voci di menu non vengono visualizzate, fare riferimento alla [Guida alla risoluzione dei problemi](~/tools/profiler/troubleshooting.md).

Viene avviato il profiler e viene avviata automaticamente la profilatura dell'applicazione.

Il profiler può essere usato per misurare la memoria e le prestazioni. Questa operazione viene raggiunta tramite le allocazioni e gli strumenti di profiler di tempo, che verrà esaminato in dettaglio nella sezione successiva.

#### <a name="saving-and-loading-profiler-sessions"></a>Salvataggio e caricamento di sessioni del profiler

Per salvare una sessione di profilatura in qualsiasi momento, scegliere **File > Salva con nome...** dalla barra dei menu del profiler. Questo consente di salvare il file in formato _MLPD_ , un formato speciale e altamente compresso per i dati di profilatura.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

Una volta installato, il Xamarin Profiler si trova nella cartella applicazioni, come illustrato nella schermata seguente:

![Apri profiler autonomo da Mac](images/applications.png)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Dopo l'installazione, è possibile trovare Xamarin Profiler applicazione nella directory dell'applicazione:

![Apri profiler autonomo da Windows](images/applications-vs.png)

-----

È possibile caricare i file con *estensione MLPD* nel profiler aprendo l'applicazione autonoma, selezionando **Scegli destinazione** e caricando il file.

Per ulteriori informazioni, vedere [generazione di file con estensione MLPD](~/tools/profiler/troubleshooting.md#gen_mlpd).

## <a name="profiler-features"></a>Funzionalità del profiler

Il Xamarin Profiler è costituito da cinque sezioni, come illustrato di seguito:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

[![Sezioni del profiler in Visual Studio per Mac](images/profiler-mac-sml.png)](images/profiler-mac.png#lightbox) 

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![Sezioni del profiler in Visual Studio](images/profiler-vs.png)](images/profiler-vs.png#lightbox)

-----

- **Barra degli strumenti** : si trova nella parte superiore del profiler, che offre opzioni per avviare/arrestare la profilatura, selezionare un processo di destinazione, visualizzare il tempo di esecuzione dell'app e selezionare le visualizzazioni divise che compongono l'applicazione del profiler.
- **Instrument list** : elenca tutti gli strumenti caricati per la sessione di profilatura.
- **Grafico dei tracciati** : questi grafici sono correlati orizzontalmente agli strumenti pertinenti nell'elenco di strumenti. Per modificare la scala, è possibile usare un dispositivo di scorrimento (visualizzato sotto Profiler temporale).
- **Area dei dettagli dello strumento** : contiene i dati visualizzati dalla visualizzazione selezionata dello strumento corrente. Queste visualizzazioni vengono esaminate più dettagliatamente nella sezione seguente.
- **Visualizzazione Inspector** : contiene le sezioni che possono essere selezionate dal controllo segmentato. Le sezioni dipendono dallo strumento selezionato e includono: Impostazioni di configurazione, statistiche, informazioni di analisi dello stack e percorso delle radici.

### <a name="allocations"></a>Allocazioni

Lo strumento allocazioni fornisce informazioni dettagliate sugli oggetti dell'applicazione durante la creazione e la Garbage Collection.

Nella parte superiore del profiler è disponibile il grafico allocazioni, che consente di visualizzare la quantità di memoria allocata a intervalli regolari durante la profilatura. Attualmente il grafico allocazioni è il numero totale di allocazioni e non le dimensioni dell'heap in quel momento. In un certo senso, non verrà mai arrestato, ma solo aumenterà. Sono inclusi gli oggetti allocati nello stack. A seconda della versione di runtime utilizzata, il grafico può avere un aspetto diverso, anche per la stessa app.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

[![Instrumentation (allocazioni)](images/allocations1.png)](images/allocations1.png#lightbox) 

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![Instrumentation (allocazioni)](images/allocations1-vs.png)](images/allocations1-vs.png#lightbox)

-----

Sono disponibili diverse visualizzazioni dati nello strumento allocazioni, che consentono agli sviluppatori di analizzare il modo in cui l'applicazione usa e libera la memoria. Queste visualizzazioni sono descritte di seguito:

- **Allocazioni** : viene visualizzato un elenco di tutte le allocazioni e i gruppi in base al nome della classe. In questo modo viene fornita una panoramica delle classi e dei metodi utilizzati, della frequenza di utilizzo e della dimensione collettiva delle classi utilizzate. Se si fa doppio clic su una classe, viene visualizzata la memoria allocata: 

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

  [![Scheda allocazioni](images/allocations3.png)](images/allocations3.png#lightbox) 

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

  [![Scheda allocazioni](images/allocations2-vs.png)](images/allocations2-vs.png#lightbox)

-----

La visualizzazione Inspector per le allocazioni fornisce opzioni per filtrare e raggruppare oggetti, fornire statistiche sulla memoria allocata e le allocazioni principali, nonché visualizzazioni per traccia dello stack e percorso alla radice.

- **Albero delle chiamate** : viene visualizzata l'intera struttura ad albero delle chiamate di tutti i thread dell'applicazione e sono incluse informazioni sulla memoria allocata in ogni nodo. Quando si seleziona un elemento nell'elenco, tutti i nodi di pari livello verranno visualizzati in grigio. È possibile espandere l'albero o fare doppio clic sull'elemento per eseguire il drill-down. Quando si visualizza questa vista dati, è possibile utilizzare la vista impostazioni di visualizzazione per modificare il modo in cui viene visualizzata. Attualmente sono disponibili due opzioni:
    1. **Albero delle chiamate invertito** : considera l'analisi dello stack dall'alto verso il basso. Si tratta di un'opzione di visualizzazione utile che indica i metodi più profondi in cui la CPU ha speso il suo tempo.
    2. **Separa per thread** : questa opzione consente di organizzare l'albero delle chiamate per thread.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

  [![Scheda albero delle chiamate](images/allocations2.png)](images/allocations2.png#lightbox) 

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

  [![Scheda albero delle chiamate](images/allocations3-vs.png)](images/allocations3-vs.png#lightbox)

-----

- **Snapshot** : in questo riquadro vengono visualizzate informazioni sugli snapshot di memoria. Per generare questi dati durante la profilatura di un'applicazione attiva, fare clic sul pulsante della _fotocamera_ sulla barra degli strumenti in corrispondenza di ogni punto in cui si desidera visualizzare la memoria mantenuta e rilasciata. È quindi possibile fare clic su ogni snapshot per esaminare cosa accade dietro le quinte. Si noti che gli snapshot possono essere effettuati solo quando si esegue la profilatura in tempo reale di un'app. 

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

  [![Scheda snapshot](images/allocations4.png)](images/allocations4.png#lightbox) 

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

  [![Scheda snapshot](images/allocations4-vs.png)](images/allocations4-vs.png#lightbox)

-----

### <a name="time-profiler"></a>Profiler tempo

Lo strumento Profiler tempo misura esattamente la quantità di tempo impiegato in ogni metodo di un'applicazione. L'applicazione viene sospesa a intervalli regolari e viene eseguita una traccia dello stack in ogni thread attivo. Ogni riga nell'area dei dettagli dello strumento Mostra il percorso di esecuzione che è stato seguito.

Il grafico dei tracciati, come illustrato nella schermata seguente, Visualizza il numero di campioni ricevuti dall'app durante l'esecuzione:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

[![Strumento Profiler tempo](images/time1.png)](images/time1.png#lightbox) 

[![Strumento Profiler tempo – elenco di esempi](images/time3.png)](images/time3.png#lightbox) 

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![Strumento Profiler tempo](images/time1-vs.png)](images/time1-vs.png#lightbox) 

[![Strumento Profiler tempo – elenco di esempi](images/time3-vs.png)](images/time3-vs.png#lightbox) 

-----

- **Albero delle chiamate** : indica la quantità di tempo impiegato in ogni metodo:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

  [![Strumento Profiler tempo-albero delle chiamate](images/time2.png)](images/time2.png#lightbox) 

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

  [![Strumento Profiler tempo-albero delle chiamate](images/time2-vs.png)](images/time2-vs.png#lightbox) 

-----

### <a name="cycles"></a>Cicli

Tramite l'utilizzo di C# e F# del codice gestito, può essere abbastanza comune e sfortunatamente piuttosto semplice creare riferimenti a oggetti che non verranno mai eliminati. Questo strumento consente di individuare tali oggetti e visualizzare i cicli a cui viene fatto riferimento nell'applicazione.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

[![Instruments (cicli)](images/cycles.m751-sml.png)](images/cycles.m751.png#lightbox) 

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![Instruments (cicli)](images/cycles-vs-sml.png)](images/cycles-vs.png#lightbox) 

-----

## <a name="profiling-applications"></a>Profilatura di applicazioni

Attualmente, è possibile profilare solo le configurazioni di debug predefinite.

Se si profila un'app con qualsiasi altra configurazione, verrà visualizzata la finestra di dialogo messaggio seguente:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

[![Finestra di dialogo di errore di profilatura](images/image001.png)](images/image001.png#lightbox) 

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![Finestra di dialogo di errore di profilatura](images/image1vs.png)](images/image1vs.png#lightbox) 

-----

Selezionare **Update (Aggiorna** ) per continuare.

### <a name="sgen-garbage-collector-and-profiling"></a>Garbage Collector e profilatura di SGen

Il Garbage Collector [SGen](https://www.mono-project.com/docs/advanced/garbage-collector/sgen/) viene usato per tutte le piattaforme Novell.

SGen è un GC generazionale, che alloca gli oggetti di un'applicazione in tre heap: Nursery, heap principale e spazio Large Object. Ciò consente un'esecuzione più rapida delle Garbage Collection. SGen è attualmente il GC predefinito per le applicazioni unificate Novell. Android e Novell. iOS.

Applicazione Novell. iOS che usa il API classica usato il GC Boehm, un Garbage Collector conservativo e non generazionale. Poiché è conservativa, è meno probabile liberare la memoria disponibile, che può produrre risultati non accurati quando si usa il profiler. Per questo motivo, non è possibile usare lo strumento allocazioni con Boehm Garbage Collector.

Mentre viene visualizzata una finestra di dialogo di messaggio se l'app usa il GC Boehm, Novell non consiglia di cambiare l'applicazione iOS esistente che usa Boehm per SGen senza un'attenta ricerca e test completi. Novell non consiglia inoltre di passare a SGen per la profilatura e quindi di tornare indietro, in quanto questi risultati non forniranno benchmark accurati di utilizzo della memoria.

Per ulteriori informazioni sulla gestione della memoria, vedere la Guida alle [procedure consigliate per la memoria e le prestazioni](~/cross-platform/deploy-test/memory-perf-best-practices.md) .

## <a name="summary"></a>Riepilogo

In questa guida è stata esaminata la profilatura e il modo in cui è vantaggioso per lo sviluppatore. È stato quindi introdotto il Xamarin Profiler, che fornisce una cronologia e informazioni sul funzionamento. Infine, sono state illustrate le funzionalità del Xamarin Profiler ed è stata esaminata l'allocazione e gli strumenti del profiler temporale.

## <a name="related-links"></a>Collegamenti correlati

- [Procedure consigliate per la memoria e le prestazioni](~/cross-platform/deploy-test/memory-perf-best-practices.md)
- [Note sulla versione](https://github.com/xamarin/release-notes-archive/blob/master/release-notes/profiler/preview/index.md)
