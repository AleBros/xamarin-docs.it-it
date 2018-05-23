---
title: Xamarin Profiler
description: Questa guida illustra le funzionalità principali del Xamarin Profiler. E risultare in Profiler, profilatura e quando deve essere utilizzati e in un flusso di lavoro standard per la profilatura delle applicazioni di Xamarin.
ms.prod: xamarin
ms.assetid: 3247fcee-6acc-470d-ab87-c1c511d67363
author: topgenorth
ms.author: toopge
ms.date: 10/27/2017
ms.openlocfilehash: 81c6a5682fc91b49a0f7495f06e7f7b6d3f76330
ms.sourcegitcommit: 9f8e7393019791bbd6af4fefaa24a1602adabb4e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/23/2018
---
# <a name="xamarin-profiler"></a>Xamarin Profiler

_Questa guida illustra le funzionalità principali del Xamarin Profiler. E risultare in Profiler, profilatura e quando deve essere utilizzati e in un flusso di lavoro standard per la profilatura delle applicazioni di Xamarin._

Esito positivo di un'applicazione dipende dall'esperienza dell'utente finale. Gli sviluppatori si potrebbe aver implementato alcune funzionalità realmente straordinario nell'app, ma se l'app è lenta o completo di arresti anomali del sistema, l'utente verrà probabilmente la rimozione.

In passato, Mono è caratterizzato da un profiler della riga di comando potente per la raccolta di informazioni sui programmi in esecuzione in runtime Mono chiamata il [profiler log Mono](http://www.mono-project.com/docs/debug+profile/profile/profiler/). Il Xamarin Profiler un'interfaccia grafica per il profiler Mono log e supporta profilatura tvOS applicazioni in Windows, iOS, tvOS e applicazioni Mac in Mac e Android, iOS e Android.

Il Xamarin Profiler è un numero di strumenti disponibili per la profilatura, le allocazioni, cicli e il Profiler di tempo. Questa guida viene descritto cosa misurano tali strumenti e la modalità e l'analisi dell'applicazione e illustra l'importanza dei dati presentati in ogni schermata.

Questa guida esamina i comuni scenari di profilatura e il profiler è stato introdotto come uno strumento che consente di analizzare e ottimizzare applicazioni iOS e Android.

## <a name="download-and-install"></a>Scaricare e installare

> [!NOTE]
> È necessario essere un [Visual Studio Enterprise](https://www.visualstudio.com/vs/compare/) sottoscrittore per sbloccare questa funzionalità in entrambi Visual Studio Enterprise in Windows o di Visual Studio per Mac su Mac.

Il Xamarin Profiler è un'applicazione autonoma ed è integrato con Visual Studio per Mac e Visual Studio per abilitare la profilatura dall'IDE.

Scaricare il pacchetto di installazione per la piattaforma:

- [**macOS**](https://dl.xamarin.com/profiler/profiler-mac.pkg)
- [**Windows**](https://dl.xamarin.com/profiler/profiler-windows.msi)

Una volta scaricato, avviare il programma di installazione per aggiungere il Xamarin Profiler nel sistema.

## <a name="profilers-and-profiling"></a>I profiler e profilatura

La profilatura è un passaggio importante e spesso trascurato nello sviluppo di applicazioni. La profilatura è una forma di **analisi dinamica programma** -analizza il programma mentre è in esecuzione e in uso. Un profiler è uno strumento di data mining di dati che raccoglie informazioni sulla complessità di tempo, l'utilizzo di determinati metodi e la memoria allocata. Un profiler consente di eseguire il drill-profondità e analizzare le metriche per individuare problemi nel codice.

Durante la progettazione e sviluppo di un'applicazione, è importante non ottimizzare in modo anomalo; vale a dire, impiegare il tempo di sviluppo del codice nelle aree che raramente si accederà. Questa è la potenza di profilatura. Un profiler fornisce una visione più comunemente utilizzati parti della codebase e consente di individuare le aree in cui è consigliabile apportare miglioramenti di tempo. Gli sviluppatori devono fare attenzione a comprendere dove la maggior parte dei casi viene impiegato nell'applicazione e la modalità di utilizzo della memoria da parte dell'applicazione.

La profilatura è utile in tutti i tipi di sviluppo, ma è particolarmente importanti in fase di sviluppo per dispositivi mobili. Codice non ottimizzato è più evidente su piattaforme mobili rispetto a computer desktop e l'esito positivo dell'app dipende dal codice efficaci e ottimizzato che viene eseguito in modo efficiente.

## <a name="xamarin-profiler"></a>Xamarin Profiler

Il Xamarin Profiler offre agli sviluppatori un modo per profilare le applicazioni da Visual Studio per Mac o Visual Studio. Il profiler raccoglie e visualizza le informazioni sull'app, che può quindi essere usato dallo sviluppatore per analizzare il comportamento dell'applicazione. Esistono diversi modi per profilare un'applicazione con il Xamarin Profiler, vale a dire profilatura della memoria e il campionamento statistico. Questi vengono eseguiti tramite le allocazioni e tempo Profiler Instrumenta rispettivamente.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

Attualmente, il Xamarin Profiler consente di testare le applicazioni di xamarin. IOS, xamarin e Xamarin.Mac sul Mac (tramite Visual Studio per Mac). Il profiler è un processo distinto dall'IDE, e in tal caso, oltre a l'avvio da Visual Studio per Mac, può essere utilizzato come applicazione autonoma per esaminare .exe e `.mlpd` file che sono stato elaborati dal [profiler log mono](http://www.mono-project.com/docs/debug+profile/profile/profiler/).

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Attualmente, il Xamarin Profiler consente di testare le app xamarin in Windows (tramite Visual Studio e Visual Studio per Mac). Il profiler è un processo distinto dall'IDE, e in tal caso, oltre a l'avvio da Visual Studio, può essere utilizzato come applicazione autonoma per esaminare .exe e `.mlpd` file che sono stato elaborati dal [profiler log mono](http://www.mono-project.com/docs/debug+profile/profile/profiler/) .

-----

<a name="Profiler_Support" />

## <a name="profiler-support"></a>Supporto del profiler

Supporto per il Xamarin Profiler è disponibile nelle piattaforme seguenti:

 - Visual Studio per Mac (con licenza Enterprise macOS)
    - Android
        - Dispositivo e l'emulatore
    - iOS
        - Dispositivo o del simulatore
    - tvOS (strumento di tempo non è supportato)
        - Dispositivo o del simulatore
    - Mac

 - Visual Studio (solo **Enterprise** versione)
    - Android
        - Dispositivo e l'emulatore
    - iOS [sperimentale]
        - Dispositivo o del simulatore
    - tvOS
        - Dispositivo o del simulatore

Si noti che è possibile **solo** profilo **Debug** configurazioni.

## <a name="profiler-basics"></a>Nozioni di base del profiler

In questa sezione vengono illustrate le parti del Xamarin Profiler e tours relative funzionalità.

### <a name="allow-profiling-in-your-app"></a>Consenti profilatura nell'App

Prima di correttamente, è possibile profilare l'app, è necessario consentire di profilatura nelle opzioni del progetto dell'app.

 - iOS:

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

  **Compilazione > iOS Debug > Abilita profilatura**

  ![](images/ios-options-mac.png "finestra di dialogo Opzioni in Visual Studio per Mac iOS")

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

  **Proprietà > iOS compilazione > Abilita profilatura**

  ![](images/ios-project-options-vs.png "finestra di dialogo Opzioni in Visual Studio iOS")

-----

 - Android:

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

  **Compilazione > Debug Android > abilitare la strumentazione per sviluppatori**

  ![Finestra di dialogo Opzioni Android in Visual Studio per Mac](images/android-project-options.png)

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

  **Compilazione > Debug Android > abilitare la strumentazione per sviluppatori**

  ![Finestra di dialogo Opzioni Android in Visual Studio per Mac](images/android-project-options-vs-sml.png)

-----

### <a name="launching-the-profiler"></a>Avvio del Profiler

Il Xamarin Profiler è possibile avviare l'IDE quando si esegue la profilatura iOS o Android applicazione o come un'applicazione autonoma.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

#### <a name="launching-from-visual-studio-for-mac"></a>Avvio da Visual Studio per Mac

1. Innanzitutto, assicurarsi che l'applicazione caricata in Visual Studio per Mac e selezionare la configurazione di Debug (impostazione predefinita).
2. Passare a **eseguire > Avvia profilatura**in Visual Studio per Mac, o **analizza > Xamarin Profiler** in Visual Studio, aprire il Profiler, come illustrato nel diagramma seguente:

  ![](images/start-profiling-xs.png "Avviare il Profiler di Visual Studio per Mac")

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

#### <a name="launching-from-visual-studio"></a>Avvio da Visual Studio

1.  Innanzitutto, assicurarsi che l'applicazione caricata in Visual Studio e selezionare la configurazione di Debug (impostazione predefinita), come specificato in precedenza.
2.  Passare a **analizza > Xamarin Profiler** in Visual Studio, aprire il Profiler, come illustrato nel diagramma seguente:

![Avviare il Profiler di Visual Studio](images/start-profiling-vs.png)

-----

Se non sono visualizzate le voci di menu, vedere il [risoluzione dei problemi guida](~/tools/profiler/troubleshooting.md).

Verrà avviato il Profiler e avvia automaticamente l'analisi di un'applicazione.

Il Profiler può essere utilizzato per misurare le prestazioni e memoria. Questo raggiunge attraverso le allocazioni e tempo Profiler strumenti, verranno analizzati in dettaglio nella sezione successiva.

#### <a name="saving-and-loading-profiler-sessions"></a>Salvataggio e caricamento del Profiler sessioni

Per salvare una sessione di profilatura in qualsiasi momento, scegliere **File > Salva con nome...**  dalla barra dei Menu del Profiler. Ciò consente di salvare il file in _mlpd_ formato, un formato altamente compresso speciale per i dati di profilatura.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

Dopo aver installato il Xamarin Profiler è reperibile nella cartella applicazioni come illustrato nella schermata seguente:

![](images/applications.png "Aprire Profiler autonomo dal Mac")

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Dopo aver installato l'applicazione di Xamarin Profiler è reperibile nella directory dell'applicazione:

![](images/applications-vs.png "Aprire Profiler autonomo da Windows ")

-----

È possibile caricare *.mlpd* file nel Profiler, aprire l'applicazione autonoma, selezionare **Scegli destinazione** e il caricamento del file.

Per ulteriori informazioni, vedere [la generazione di file .mlpd ](~/tools/profiler/troubleshooting.md#gen_mlpd).

## <a name="profiler-features"></a>Funzionalità del profiler

Il Xamarin Profiler è composto da cinque sezioni, come illustrato di seguito:

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

[![](images/profiler-mac-sml.png "Sezioni del profiler di Visual Studio per Mac")](images/profiler-mac.png#lightbox) 

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![](images/profiler-vs.png "Sezioni del profiler di Visual Studio")](images/profiler-vs.png#lightbox)

-----

- **Barra degli strumenti** : nella parte superiore del profiler, questa opzione offre opzioni per avviare o arrestare la profilatura, selezionare un processo di destinazione, visualizzare il tempo di esecuzione dell'app e selezionare le viste di divisione che costituiscono l'applicazione del profiler.
- **Elenco di instrumentare** : Elenca tutti gli strumenti caricati per la sessione di profilatura.
- **Tracciare grafico** – questi grafici orizzontalmente riguardano gli strumenti pertinenti nell'elenco di strumentazione. Un dispositivo di scorrimento (mostrato sotto il Profiler di tempo) consente di modificare la scala.
- **Instrumentare area dettaglio** -contiene i dati visualizzati dalla vista selezionata dello strumento corrente. Verranno esaminati in dettaglio nella sezione seguente queste viste.
- **Controllo visualizzazione** – contiene le sezioni che possono essere selezionate dal controllo segmentato. Le sezioni sono dipendenti dallo strumento selezionato e include: le impostazioni di configurazione, le statistiche, le informazioni di traccia dello Stack e percorso radice.

### <a name="allocations"></a>Allocazioni

Lo strumento di allocazioni fornisce informazioni dettagliate sugli oggetti nell'applicazione, come la creazione di garbage collection.

Nella parte superiore del profiler è il grafico delle allocazioni, che visualizza la quantità di memoria allocata a intervalli regolari durante la profilatura. Attualmente il grafico di allocazioni è il numero totale di allocazioni e non alle dimensioni dell'heap a questo punto nel tempo. In un certo senso, mai spostarlo verso il basso, solo aumenterà. Ciò include gli oggetti allocati nello stack. A seconda della versione di runtime utilizzata, il grafico può risultare diverso, anche per la stessa app.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

[![](images/allocations1.png "Strumento di allocazioni")](images/allocations1.png#lightbox) 

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![](images/allocations1-vs.png "Strumento di allocazioni")](images/allocations1-vs.png#lightbox)

-----

Sono disponibili visualizzazioni di dati diversi in strumento di allocazioni, che consentono agli sviluppatori di analizzare come le applicazioni utilizzando e liberare memoria. Queste viste sono descritti di seguito:

 -   **Le allocazioni** : consente di visualizzare un elenco di tutte le allocazioni e li raggruppa in base al nome di classe. Ciò fornisce una panoramica di classi e metodi utilizzati, la frequenza con cui vengono utilizzati e la dimensione collettiva delle classi usate. Facendo doppio clic su una classe mostrerà la memoria allocata: 

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

  [![](images/allocations3.png "Scheda allocazioni")](images/allocations3.png#lightbox) 

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

  [![](images/allocations2-vs.png "Scheda allocazioni")](images/allocations2-vs.png#lightbox)

-----

La vista di controllo per le allocazioni fornisce opzioni per filtrare e raggruppare gli oggetti, forniscono statistiche sulla memoria allocata e le allocazioni superiore, nonché le visualizzazioni per l'analisi dello Stack e il percorso radice.

 -   **Struttura chiamate** : consente di visualizzare l'intera struttura delle chiamate di tutti i thread nell'applicazione e include informazioni sulla quantità di memoria allocato in ogni nodo. Quando viene selezionato un elemento nell'elenco, tutti i nodi di pari livello verranno visualizzati in grigio. È possibile espandere l'albero o doppio clic sull'elemento per il drill-down al suo interno. Quando si visualizza la visualizzazione di dati, è possibile utilizzare il controllo delle impostazioni di visualizzazione per modificare il modo in cui che viene visualizzato. Sono disponibili due opzioni:
    1.  **Albero delle chiamate invertito** – si considera la traccia dello stack dall'alto verso il basso. Questa è un'opzione di visualizzazione semplice in quanto indica i metodi più in basso in cui la CPU è stato spesa ora.
    2.  **Separato da thread** : questa opzione consente di organizzare l'albero delle chiamate dal thread.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

  [![](images/allocations2.png "Scheda albero delle chiamate")](images/allocations2.png#lightbox) 

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

  [![](images/allocations3-vs.png "Scheda albero delle chiamate")](images/allocations3-vs.png#lightbox)

-----

 -   **Gli snapshot** : questo riquadro sono visualizzate informazioni sugli snapshot di memoria. Per generare queste durante la profilatura di un'applicazione in tempo reale, fare clic su di _fotocamera_ sulla barra degli strumenti in ogni punto che si desidera vedere quali memoria viene mantenuta e rilasciata. È quindi possibile fare clic su ogni snapshot per esplorare ciò che accade dietro le quinte. Si noti che gli snapshot possono essere eseguiti solo quando è attiva un'app di profilatura. 

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

  [![](images/allocations4.png "Scheda snapshot")](images/allocations4.png#lightbox) 

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

  [![](images/allocations4-vs.png "Scheda snapshot")](images/allocations4-vs.png#lightbox)

-----

### <a name="time-profiler"></a>Profiler di tempo

Lo strumento di Profiler di tempo misura esattamente come tempo speso per ogni metodo di un'applicazione. L'applicazione è stata sospesa a intervalli regolari e viene eseguita un'analisi dello stack in ogni thread attivo. Ogni riga nell'area dei dettagli di instrumentare Mostra il percorso di esecuzione che è stato visitato.

Il grafico, come illustrato nella schermata seguente, viene visualizzato il numero di campioni ricevuti dall'app durante l'esecuzione:

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

[![Strumento di Profiler ora](images/time1.png)](images/time1.png#lightbox) 

[![Ora di strumentazione del Profiler: elenco di esempi](images/time3.png)](images/time3.png#lightbox) 

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Strumento di Profiler ora](images/time1-vs.png)](images/time1-vs.png#lightbox) 

[![Ora di strumentazione del Profiler: elenco di esempi](images/time3-vs.png)](images/time3-vs.png#lightbox) 

-----

- **Struttura chiamate** – Mostra tempo impiegato in ogni metodo:

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

  [![](images/time2.png "Ora di strumentazione del Profiler: struttura delle chiamate")](images/time2.png#lightbox) 

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

  [![](images/time2-vs.png "Ora di strumentazione del Profiler: struttura delle chiamate")](images/time2-vs.png#lightbox) 

-----

### <a name="cycles"></a>Cicli

Tramite l'utilizzo di codice c# e F # gestiti, può essere molto comune e purtroppo molto semplice creare riferimenti a oggetti che non verranno mai eliminati. Questo intrument consente di individuare tali oggetti e visualizzare i cicli di cui viene fatto riferimento nell'applicazione.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Strumento di cicli](images/cycles-vs.png)](images/time1-vs.png#lightbox) 

-----

## <a name="profiling-applications"></a>Profilatura di applicazioni

Attualmente, possono eseguire il profiling solo le configurazioni di Debug predefinito.

Se si profila un'app con qualsiasi altra configurazione, verrà visualizzata la finestra di messaggio seguenti:


# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

[![Finestra di dialogo Errore di profilatura](images/image001.png)](images/image001.png#lightbox) 

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![](images/image1vs.png "Finestra di dialogo Errore di profilatura")](images/image1vs.png#lightbox) 

-----

Selezionare **aggiornamento** per continuare.

### <a name="sgen-garbage-collector-and-profiling"></a>SGen Garbage Collector e profilatura

Il [SGen](http://www.mono-project.com/docs/advanced/garbage-collector/sgen/) garbage collector non viene utilizzato per tutte le piattaforme di Xamarin.

SGen è un catalogo globale generazionale, che consente di allocare gli oggetti di un'applicazione in tre heap, ovvero vivaio, Heap principale e lo spazio di oggetto di grandi dimensioni. Ciò consente l'esecuzione più veloce di garbage collection. SGen è attualmente il Garbage Collector predefinito per xamarin e le applicazioni unificata di xamarin. IOS.

Applicazione di xamarin. IOS usando l'API classico utilizzato il Garbage Collector Boehm – un garbage collector non generazionale, conservativo. Come è conservativa, è meno probabile che liberare la memoria disponibile, che può portare a risultati non accurati quando si usa il profiler. Per questo motivo, è Impossibile utilizzare lo strumento di allocazioni a Boehm garbage collector.

Mentre se l'app Usa il Garbage Collector Boehm, verrà richiesto con una finestra di dialogo del messaggio, Xamarin non è consigliabile passare applicazione iOS esistenti che utilizzano Boehm per SGen senza ricerca attenzione e a test approfonditi. Xamarin anche non consigliabile passare al SGen per il profiling e quindi passare nuovamente, come i risultati non offriranno i benchmark accurati dell'utilizzo della memoria.

Per ulteriori informazioni sulla gestione della memoria, consultare il [memoria e prestazioni ottimali](~/cross-platform/deploy-test/memory-perf-best-practices.md) Guida.

## <a name="summary"></a>Riepilogo

In questa guida è stato esaminato profiling è e come è vantaggioso per gli sviluppatori. Quindi, abbiamo introdotto il Xamarin Profiler, che fornisce alcune informazioni sulla cronologia e nel funzionamento. Infine si toured le funzionalità del Xamarin Profiler e sono stati illustrati le allocazioni e strumenti Profiler ora.

## <a name="related-links"></a>Collegamenti correlati

- [Procedure consigliate per la memoria e prestazioni](~/cross-platform/deploy-test/memory-perf-best-practices.md)
- [Note sulla versione](https://developer.xamarin.com/releases/profiler/preview/)
