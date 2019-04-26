---
title: Xamarin Profiler
description: Questa guida illustra le funzionalità principali del Profiler Xamarin. E apparire in Profiler, profilatura e quando devono essere usati e in un flusso di lavoro standard per la profilatura delle applicazioni Xamarin.
ms.prod: xamarin
ms.assetid: 3247fcee-6acc-470d-ab87-c1c511d67363
author: lobrien
ms.author: laobri
ms.date: 06/03/2018
ms.openlocfilehash: 15739ff191953e4730d44c6ad9f63dccb9a0017e
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61377107"
---
# <a name="xamarin-profiler"></a>Xamarin Profiler

_Questa guida illustra le funzionalità principali del Profiler Xamarin. E apparire in Profiler, profilatura e quando devono essere usati e in un flusso di lavoro standard per la profilatura delle applicazioni Xamarin._

Esito positivo di un'applicazione dipende dall'esperienza dell'utente finale. Gli sviluppatori si potrebbe aver implementato alcune funzionalità davvero incredibile nell'app, ma se l'app è lenta o completo di arresti anomali del sistema, l'utente sarà probabilmente sbarazzarcene.

Mono è presentato in passato, un potente profiler della riga di comando per la raccolta di informazioni sui programmi in esecuzione in runtime di Mono denominato il [profiler di Mono log](https://www.mono-project.com/docs/debug+profile/profile/profiler/). Un'interfaccia grafica per il profiler di Mono di log e supporta la profilatura di applicazioni tvOS su Windows, iOS, tvOS e le applicazioni Mac in Mac e Android, iOS e Android di Xamarin Profiler.

Il Profiler Xamarin offre numerosi strumenti per la profilatura, le allocazioni, cicli e Profiler di tempo. Questa guida esamina che cosa misure di questi strumenti e di analizzare l'applicazione e chiarisce il significato dei dati presentati in ogni schermata.

Questa guida esamina i comuni scenari di profilatura e il profiler è stato introdotto come uno strumento per analizzare e ottimizzare applicazioni iOS e Android.

## <a name="download-and-install"></a>Scaricare e installare

> [!NOTE]
> È necessario essere un [Visual Studio Enterprise](https://visualstudio.microsoft.com/vs/compare/) sottoscrittore per rendere disponibile questa funzionalità in entrambi Visual Studio Enterprise su Windows o Visual Studio per Mac in un computer Mac.

Il Profiler Xamarin è un'applicazione autonoma e integrato con Visual Studio per Mac e Visual Studio abilitare la profilatura dall'IDE.

Scaricare il pacchetto di installazione per la piattaforma in uso:

- [**macOS**](https://dl.xamarin.com/profiler/profiler-mac.pkg)
- [**Windows**](https://dl.xamarin.com/profiler/profiler-windows.msi)

Al termine del download, avviare il programma di installazione per aggiungere il Profiler Xamarin nel sistema.

## <a name="profilers-and-profiling"></a>I profiler e profilatura

La profilatura è un passaggio importante e spesso trascurato nello sviluppo di applicazioni. La profilatura è una forma di **analisi dinamica programma** -che analizza il programma mentre è in esecuzione e in uso. Un profiler è uno strumento di data mining dei dati che raccoglie informazioni sulla complessità di tempo, l'utilizzo di determinati metodi e la memoria viene allocata. Un profiler consente di eseguire il drill-profonde e analizzare le metriche per individuare le aree problematiche nel codice.

Durante la progettazione e sviluppo di un'applicazione, è importante non ottimizzare in modo anomalo; vale a dire, impiega tempo per lo sviluppo di codice nelle aree che raramente si accederà. Questa è la potenza dell'analisi. Un profiler fornisce informazioni dettagliate più comunemente usati parti della codebase e consente di individuare le aree in cui è consigliabile dedicare miglioramenti assicurandosi di tempo. Gli sviluppatori devono prestare attenzione a comprendere dove viene impiegato la maggior parte del tempo nell'applicazione e modalità di utilizzo della memoria dell'applicazione.

La profilatura è utile in tutti i tipi di sviluppo, ma è particolarmente importante nello sviluppo per dispositivi mobili. Codice non ottimizzato è molto più evidenti nelle piattaforme per dispositivi mobili rispetto a computer desktop e il successo della tua app dipende dal codice accattivante e ottimizzato che viene eseguito in modo efficiente.

## <a name="xamarin-profiler"></a>Xamarin Profiler

Il Profiler Xamarin offre agli sviluppatori un modo per sottoporre a profilatura applicazioni da Visual Studio per Mac o Visual Studio. Il profiler raccoglie e visualizza le informazioni sull'app, che è quindi utilizzabile dallo sviluppatore per analizzare il comportamento di un'applicazione. Esistono molti modi diversi per profilare un'applicazione con il Profiler Xamarin, vale a dire profilatura della memoria e il campionamento statistico. Queste vengono eseguite tramite le allocazioni e Profiler ora consente di instrumentare rispettivamente.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

Attualmente, il Profiler Xamarin è utilizzabile per testare le applicazioni xamarin. IOS, xamarin. Android e xamarin. Mac in computer Mac (tramite Visual Studio per Mac). Il profiler è un processo separato dall'IDE, e quindi, oltre a l'avvio da Visual Studio per Mac, può essere utilizzato come applicazione autonoma per esaminare .exe e `.mlpd` i file che sono stati generati dal [profiler di mono log](https://www.mono-project.com/docs/debug+profile/profile/profiler/).

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Attualmente, il Profiler Xamarin è utilizzabile per testare le app xamarin. Android in Windows (tramite Visual Studio e Visual Studio per Mac). Il profiler è un processo separato dall'IDE, e quindi, oltre a l'avvio da Visual Studio, può essere utilizzato come applicazione autonoma per esaminare .exe e `.mlpd` i file che sono stati generati dal [profiler di mono log](https://www.mono-project.com/docs/debug+profile/profile/profiler/) .

-----

<a name="Profiler_Support" />

## <a name="profiler-support"></a>Supporto di Profiler

Supporto per il Profiler Xamarin è disponibile nelle piattaforme seguenti:

 - Visual Studio per Mac (con licenza Enterprise, macOS)
    - Android
        - Dispositivo e l'emulatore
    - iOS
        - Dispositivo e del simulatore
    - tvOS (fase di strumentazione non è supportato)
        - Dispositivo e del simulatore
    - Mac

 - Visual Studio (solo **Enterprise** versione)
    - Android
        - Dispositivo e l'emulatore
    - iOS [sperimentale]
        - Dispositivo e del simulatore
    - tvOS
        - Dispositivo e del simulatore

Si noti che è possibile **soltanto** profilo **Debug** configurazioni.

## <a name="profiler-basics"></a>Nozioni di base di Profiler

In questa sezione vengono illustrate le parti del Profiler Xamarin e indirizzate le relative funzionalità.

### <a name="allow-profiling-in-your-app"></a>Consenti profilatura nell'App

Prima è stato possibile profilare l'app, è necessario consentire la profilatura nelle opzioni di progetto dell'app.

 - iOS:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

  **Compilazione > Debug iOS > Abilita profilatura**

  ![](images/ios-options-mac.png "finestra di dialogo Opzioni in Visual Studio per Mac iOS")

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

  **Proprietà > compilazione iOS > Abilita profilatura**

  ![](images/ios-project-options-vs.png "finestra di dialogo Opzioni in Visual Studio di iOS")

-----

 - Android:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

  **Compilazione > Debug Android > Abilita la strumentazione per sviluppatori**

  ![Finestra di dialogo Opzioni Android in Visual Studio per Mac](images/android-project-options.png)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

  **Compilazione > Debug Android > Abilita la strumentazione per sviluppatori**

  ![Finestra di dialogo Opzioni Android in Visual Studio per Mac](images/android-project-options-vs-sml.png)

-----

### <a name="launching-the-profiler"></a>L'avvio di Profiler

Il Profiler Xamarin possono essere avviati dall'IDE quando si esegue la profilatura dell'applicazione Android o iOS o come applicazione autonoma.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

#### <a name="launching-from-visual-studio-for-mac"></a>Avviato da Visual Studio per Mac

1. In primo luogo, assicurarsi che l'applicazione caricata in Visual Studio per Mac e selezionare la configurazione di Debug (impostazione predefinita).
2. Passare a **Esegui > Avvia profilatura**in Visual Studio per Mac, o **analizza > Xamarin Profiler** in Visual Studio, aprire il Profiler, come illustrato nel diagramma seguente:

  ![](images/start-profiling-xs.png "Avviare il Profiler da Visual Studio per Mac")

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

#### <a name="launching-from-visual-studio"></a>L'avvio da Visual Studio

1.  In primo luogo, assicurarsi che l'applicazione caricata in Visual Studio e selezionare la configurazione di Debug (impostazione predefinita), come specificato in precedenza.
2.  Passare a **analizza > Xamarin Profiler** in Visual Studio, aprire il Profiler, come illustrato nel diagramma seguente:

![Avviare il Profiler da Visual Studio](images/start-profiling-vs.png)

-----

Se non sono visualizzate le voci di menu, vedere la [Guida alla risoluzione dei](~/tools/profiler/troubleshooting.md).

Verrà avviato il Profiler e avvia automaticamente la profilatura dell'applicazione.

Il Profiler è utilizzabile per misurare la memoria e prestazioni. Questo raggiunge tramite le allocazioni e Profiler ora gli strumenti, che verranno analizzati in dettaglio nella sezione successiva.

#### <a name="saving-and-loading-profiler-sessions"></a>Salvataggio e caricamento di sessioni di Profiler

Per salvare una sessione di profilatura in qualsiasi momento, scegliere **File > Salva con nome...**  dalla barra dei Menu Profiler. Ciò consente di salvare il file nella _mlpd_ formato, un formato altamente compresso speciale per i dati di profilatura.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

Dopo aver installato il Profiler Xamarin è reperibile nella cartella di applicazioni come illustrato nello screenshot seguente:

![](images/applications.png "Aprire Profiler autonomi dal Mac")

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Dopo aver installato l'applicazione Xamarin Profiler è reperibile nella directory dell'applicazione:

![](images/applications-vs.png "Apri Profiler autonomo di Windows ")

-----

È possibile caricare *.mlpd* i file nei Profiler aprendo l'applicazione autonoma, selezionando **Scegli destinazione** e il caricamento del file.

Per altre informazioni, vedere [generazione di file .mlpd ](~/tools/profiler/troubleshooting.md#gen_mlpd).

## <a name="profiler-features"></a>Funzionalità di Profiler

Il Profiler Xamarin è composto da cinque sezioni seguenti come illustrato di seguito:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

[![](images/profiler-mac-sml.png "Sezioni di Profiler in Visual Studio per Mac")](images/profiler-mac.png#lightbox) 

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![](images/profiler-vs.png "Sezioni di Profiler in Visual Studio")](images/profiler-vs.png#lightbox)

-----

- **Sulla barra degli strumenti** – si trova nella parte superiore del profiler, questa opzione offre opzioni per avviare o arrestare la profilatura, selezionare un processo di destinazione, visualizzare il tempo di esecuzione dell'app e selezionare le doppie visualizzazioni che costituiscono l'applicazione di profiler.
- **Instrumentare elenco** : Elenca tutti gli strumenti caricati per la sessione di profilatura.
- **Tracciare grafico** : questi grafici correlati in senso orizzontale a strumenti pertinenti nell'elenco di strumentazione. Un dispositivo di scorrimento (mostrato sotto Profiler ora) utilizzabile per modificare la scala.
- **Instrumentare area dettaglio** -contiene dati da visualizzare dalla vista selezionata dello strumento corrente. Esamineremo queste visualizzazioni in modo più dettagliato nella sezione seguente.
- **Visualizzazione Inspector** – contiene le sezioni che possono essere selezionate dal controllo segmentato. Le sezioni sono dipendenti dallo strumento selezionato e include: Le impostazioni di configurazione, le statistiche, le informazioni di traccia dello Stack e il percorso delle radici.

### <a name="allocations"></a>Allocazioni

Lo strumento usato per le allocazioni fornisce informazioni dettagliate sugli oggetti nell'applicazione poiché essi vengono creati e sottoposti a garbage collection.

Nella parte superiore del profiler è il grafico di allocazioni, che consente di visualizzare la quantità di memoria allocata a intervalli regolari durante la profilatura. Attualmente il grafico di allocazioni è il numero totale di allocazioni e non le dimensioni dell'heap a quel punto nel tempo. In un certo senso, non rivolgerà mai verso il basso, solo che mai aumenterà. Ciò include gli oggetti allocati nello stack. A seconda della versione runtime usata, il grafico può risultare diverso – anche per l'app stessa.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

[![](images/allocations1.png "Instrumentare le allocazioni")](images/allocations1.png#lightbox) 

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![](images/allocations1-vs.png "Instrumentare le allocazioni")](images/allocations1-vs.png#lightbox)

-----

Sono disponibili viste di dati diverso nell'atto di allocazioni, che consentono agli sviluppatori di analizzare come le applicazioni vengono usando e liberare la memoria. Queste viste sono descritti di seguito:

 -   **Le allocazioni** : consente di visualizzare un elenco di tutte le allocazioni e li raggruppa in base al nome di classe. Ciò offre una panoramica completa di classi e metodi in uso, con quale frequenza vengono usati e le dimensioni collettive delle classi usate. Facendo doppio clic su una classe mostrerà la memoria allocata: 

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

  [![](images/allocations3.png "Scheda allocazioni")](images/allocations3.png#lightbox) 

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

  [![](images/allocations2-vs.png "Scheda allocazioni")](images/allocations2-vs.png#lightbox)

-----

La vista di controllo per le allocazioni fornisce opzioni per il filtro e raggruppamento di oggetti, che fornisce le statistiche di memoria allocata e i principali allocazioni, nonché le visualizzazioni per analisi dello Stack e il percorso radice.

 -   **Albero delle chiamate** – consente di visualizzare l'intera struttura delle chiamate di tutti i thread nell'applicazione e include informazioni sulla quantità di memoria allocato in ogni nodo. Quando viene selezionato un elemento nell'elenco, tutti i nodi di pari livello verranno visualizzati in grigio. È possibile espandere l'albero o doppio clic sull'elemento per il drill-down al suo interno. Quando si visualizzano questa vista dati, la visualizzazione di controllo di impostazioni è utilizzabile per modificare il modo in cui che viene presentato. Attualmente sono disponibili due opzioni:
    1.  **Albero delle chiamate invertito** – ciò prende in considerazione l'analisi dello stack dall'alto verso il basso. Questa è un'opzione comoda visualizzazione poiché indica i metodi più in basso in cui la CPU ha stato trascorre il tempo.
    2.  **Separato da thread** : questa opzione consente di organizzare l'albero delle chiamate dal thread.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

  [![](images/allocations2.png "Scheda albero delle chiamate")](images/allocations2.png#lightbox) 

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

  [![](images/allocations3-vs.png "Scheda albero delle chiamate")](images/allocations3-vs.png#lightbox)

-----

 -   **Gli snapshot** : questo riquadro vengono visualizzate informazioni sugli snapshot di memoria. Per generare questi criteri durante la profilatura di un'applicazione in tempo reale, scegliere il _fotocamera_ pulsante sulla barra degli strumenti in ogni punto che si desidera vedere quali memoria viene mantenuta e rilasciata. È quindi possibile fare clic su ogni snapshot per esaminare ciò che accade dietro le quinte. Si noti che gli snapshot possono essere eseguiti solo quando è attiva la profilatura di un'app. 

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

  [![](images/allocations4.png "Scheda snapshot")](images/allocations4.png#lightbox) 

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

  [![](images/allocations4-vs.png "Scheda snapshot")](images/allocations4-vs.png#lightbox)

-----

### <a name="time-profiler"></a>Profiler di tempo

Lo strumento Profiler ora misura esattamente quanto tempo viene impiegato in ogni metodo di un'applicazione. L'applicazione viene sospesa a intervalli regolari e viene eseguita un'analisi dello stack in ogni thread attivo. Ogni riga nell'area dei dettagli di instrumentare Mostra il percorso di esecuzione che è stato visitato.

Il grafico, come illustrato nello screenshot seguente, viene visualizzato il numero di campioni ricevuti dall'app durante l'esecuzione:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

[![Strumento Profiler ora](images/time1.png)](images/time1.png#lightbox) 

[![Strumento Profiler ora: elenco di esempi](images/time3.png)](images/time3.png#lightbox) 

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![Strumento Profiler ora](images/time1-vs.png)](images/time1-vs.png#lightbox) 

[![Strumento Profiler ora: elenco di esempi](images/time3-vs.png)](images/time3-vs.png#lightbox) 

-----

- **Albero delle chiamate** – Mostra periodo di tempo impiegato in ogni metodo:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

  [![](images/time2.png "Tempo Profiler instrumentare – albero delle chiamate")](images/time2.png#lightbox) 

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

  [![](images/time2-vs.png "Tempo Profiler instrumentare – albero delle chiamate")](images/time2-vs.png#lightbox) 

-----

### <a name="cycles"></a>Cicli

Tramite l'uso di C# e F# codice gestito, può risultare piuttosto comune e Sfortunatamente è piuttosto semplice creare riferimenti a oggetti che non verranno mai essere eliminati. Questo strumento consente di individuare gli oggetti e visualizzare i cicli di cui viene fatto riferimento nell'applicazione.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

[![I cicli di strumentazione](images/cycles.m751-sml.png)](images/cycles.m751.png#lightbox) 

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![I cicli di strumentazione](images/cycles-vs-sml.png)](images/cycles-vs.png#lightbox) 

-----

## <a name="profiling-applications"></a>Profilatura di applicazioni

Attualmente, solo le configurazioni di Debug predefinito possono essere profilate.

Se si profila un'app con qualsiasi altra configurazione, verrà visualizzata la finestra di dialogo di messaggio seguente:


# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

[![Finestra di dialogo Errore di profilatura](images/image001.png)](images/image001.png#lightbox) 

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![](images/image1vs.png "Finestra di dialogo Errore di profilatura")](images/image1vs.png#lightbox) 

-----

Selezionare **Update** per continuare.

### <a name="sgen-garbage-collector-and-profiling"></a>SGen Garbage Collector e profilatura

Il [SGen](https://www.mono-project.com/docs/advanced/garbage-collector/sgen/) garbage collector non viene usato per tutte le piattaforme di Xamarin.

SGen è un GC generazionale, che consente di allocare oggetti di un'applicazione in tre heap, ovvero Nursery, Heap principale e lo spazio di oggetti di grandi dimensioni. Ciò consente l'esecuzione più veloce di garbage collection. SGen è attualmente il Garbage Collector predefinito per xamarin. Android e delle applicazioni unificata di xamarin. IOS.

Applicazione xamarin. IOS tramite l'API classica utilizzato il Garbage Collector Boehm – un garbage collector non generazionale, conservativo. Come è conservativo, è meno probabile che liberare la memoria disponibile, che può portare a risultati non accurati quando si usa il profiler. Per questo motivo, lo strumento usato per le allocazioni non è utilizzabile con il garbage collector Boehm.

Mentre verrà richiesto con una finestra di dialogo di messaggio se l'app Usa il Garbage Collector Boehm, Xamarin non è consigliabile passare esistente dell'applicazione iOS che usano Boehm da SGen senza un'attenta ricerca e di eseguire test approfonditi. Xamarin inoltre non è consigliabile passare a SGen per la profilatura e quindi il ritorno, perché questi risultati non fornirà i benchmark accurati dell'utilizzo della memoria.

Per ulteriori informazioni sulla gestione della memoria, vedere la [consigliate per le prestazioni e memoria](~/cross-platform/deploy-test/memory-perf-best-practices.md) Guida.

## <a name="summary"></a>Riepilogo

In questa guida è stato profiling è e come è vantaggioso per gli sviluppatori. Quindi, abbiamo introdotto il Profiler Xamarin, che fornisce alcune informazioni sulla cronologia e in funzionamento. Infine si hanno le caratteristiche del Profiler Xamarin ed è stata esaminata la allocazioni e gli strumenti Profiler ora.

## <a name="related-links"></a>Collegamenti correlati

- [Procedure consigliate per la memoria e prestazioni](~/cross-platform/deploy-test/memory-perf-best-practices.md)
- [Note sulla versione](https://developer.xamarin.com/releases/profiler/preview/)
