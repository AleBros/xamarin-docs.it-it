---
title: DevOps con Xamarin
ms.assetid: ff978cc2-5a25-46d6-921b-e51adaa65992
author: davidortinau
ms.author: daortin
manager: crdun
ms.workload:
- xamarin
ms.date: 10/23/2018
ms.openlocfilehash: bd1a728dfe14ff9243402bedae74db7be93bf466
ms.sourcegitcommit: db422e33438f1b5c55852e6942c3d1d75dc025c4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/24/2020
ms.locfileid: "78292232"
---
# <a name="devops-with-xamarin"></a>DevOps con Xamarin

Xamarin consente di compilare le app per dispositivi mobili multipiattaforma destinate ad Android, iOS e Windows con C#, .NET e Visual Studio. Xamarin consente di condividere la maggior parte del codice tra le piattaforme e solo una piccola percentuale di codice rimane specifica della piattaforma.

Lo sviluppo di app per piattaforme moderne comporta molte più attività rispetto alla semplice scrittura di codice. Queste attività definite DevOps (Development + Operations) si estendono per il ciclo di vita completo di un'app e includono pianificazione e rilevamento del lavoro, progettazione e implementazione di codice, gestione di un repository di codice sorgente, esecuzione di compilazioni, gestione di integrazioni e distribuzioni continue, test (tra cui unit test e test dell'interfaccia utente), esecuzione di varie forme di diagnostica in ambienti di sviluppo e produzione e monitoraggio delle prestazioni delle app e comportamenti dell'utente in tempo reale tramite telemetria e analisi.

Visual Studio insieme a Azure DevOps Services e Team Foundation Server offre un'ampia gamma di funzionalità DevOps. Molti di queste sono interamente applicabili ai progetti multipiattaforma. Ciò è particolarmente vero con le app Xamarin poiché vengono compilate con C# e .NET per i quali sono compilati alcuni strumenti DevOps. Altri strumenti richiedono una stretta integrazione con gli ambienti di runtime e di compilazione. Poiché le app Xamarin vengono eseguite su piattaforme non Windows e usano l'implementazione Mono di .NET, Xamarin offre strumenti specializzati per determinate esigenze.

Le tabelle seguenti identificano le funzionalità DevOps di Visual Studio di cui è possibile prevedere il corretto funzionamento con un progetto Xamarin e quelle che invece presentano limitazioni. Per informazioni dettagliate sulle funzionalità fare riferimento alla documentazione collegata.

## <a name="agile-tools"></a>Strumenti Agile:

Collegamento di riferimento: **[About Agile tools and Agile project management](/azure/devops/boards/backlogs/backlogs-overview?view=azure-devops)** (Informazioni sugli strumenti e la gestione di progetti Agile)

Commento generale: tutte le funzionalità di pianificazione e traccia sono indipendenti dal tipo di progetto e dai linguaggi di codifica.

|Caratteristica|Supportato con Xamarin|Commenti aggiuntivi|
|-------------|----------------------------|-------------------------|
|Gestione di backlog e sprint|Sì||
|Verifica del lavoro|Sì||
|Collaborazione nella chat team|Sì||
|Bacheche Kanban|Sì||
|Segnalare e visualizzare lo stato di avanzamento|Sì||

## <a name="modeling"></a>Modellazione

Collegamento di riferimento: **[Analizzare e modellare l'architettura](/visualstudio/modeling/analyze-and-model-your-architecture)**

Le funzionalità di progettazione sono indipendenti dal linguaggio di codifica o funzionano con i linguaggi .NET come C#. Per informazioni sugli aspetti correlati al codice, vedere [Ruoli dei diagrammi dell'architettura e della modellazione nello sviluppo del software](/visualstudio/modeling/scenario-change-your-design-using-visualization-and-modeling#ModelingDiagramsTools).

|Caratteristica|Supportato con Xamarin|Commenti aggiuntivi|
|-------------|----------------------------|-------------------------|
|Diagrammi sequenza|Sì||
|Grafici delle dipendenze|Sì||
|Gerarchia di chiamata|Sì||
|Progettazione classi|Sì||
|Esplora architettura|Sì||
|Diagrammi UML (caso di utilizzo, attività, classe, componente, sequenza e DSL)|Sì||
|Diagrammi livello|Sì||
|Convalida dei livelli|Sì||

## <a name="code"></a>Codice

|Caratteristica|Supportato con Xamarin|Commenti aggiuntivi|
|-------------|----------------------------|-------------------------|
|[Usare controllo della versione di Team Foundation (TFVC)](/azure/devops/repos/tfvc/overview?view=vsts) o Azure Repos|Sì||
|[Introduzione a Git in Azure Repos](/azure/devops/repos/git/gitquickstart?view=vsts&tabs=visual-studio)|Sì||
|[Migliorare la qualità del codice](/visualstudio/test/improve-code-quality)|Sì||
|[Trovare le modifiche apportate al codice e altri elementi della cronologia](/visualstudio/ide/find-code-changes-and-other-history-with-codelens)|Sì|Ad eccezione dei limiti specifici tra le piattaforme dove l'implementazione viene risolta solo dopo la fase di esecuzione.|
|[Usare le mappe del codice per eseguire il debug delle applicazioni](/visualstudio/modeling/use-code-maps-to-debug-your-applications)|Sì||

## <a name="build"></a>Compila

Collegamento di riferimento: **[Azure Pipelines](/azure/devops/pipelines/index?view=vsts)**

|Caratteristica|Supportato con Xamarin|Commenti aggiuntivi|
|-------------|----------------------------|-------------------------|
|Server TFS locale|Sì|È necessario che nei computer di compilazione sia installato Xamarin e che i computer possano essere collegati a un computer OSX per la compilazione con iOS. Vedere [Use TFVC](/azure/devops/repos/tfvc/overview?view=vsts) (Usare il controllo della versione di Team Foundation)|
|Server di compilazione locale collegato a Azure Pipelines|Sì|Vedere [Build and release agents](/azure/devops/pipelines/agents/agents?view=vsts) (Agenti di compilazione e versione) per le istruzioni.|
|Servizio controller ospitato di Azure Pipelines|Sì|Vedere [Build your Xamarin app](/azure/devops/pipelines/languages/xamarin?view=vsts&tabs=vsts) (Compilare l'app Xamarin).|
|Definizioni di compilazione con pre e post script|Sì||
|Integrazione continuata incluse le archiviazioni gestite|Sì|Archiviazioni gestite per TFVC solo quando Git elabora un modello di richiesta di pull anziché le archiviazioni.|

## <a name="test"></a>Test

|Caratteristica|Supportato con Xamarin|Commenti aggiuntivi|
|-------------|----------------------------|-------------------------|
|Pianificazione dei test, creazione di test case e organizzazione di gruppi di test|Sì||
|Test manuali|Sì||
|Test Manager (registrazione e riproduzione di test)|Sì|Solo dispositivi Windows ed emulatori Android da Visual Studio.|
|Code coverage|n/d||
|[Eseguire unit test del codice](/visualstudio/test/unit-test-your-code/)|Sì|Per destinazioni Windows e Android, è possibile usare gli strumenti incorporati MSTest. Per eseguire unit test in Windows, Android e iOS, Xamarin consiglia NUnit. Vedere [Use TFVC](/azure/devops/repos/tfvc/overview?view=vsts) (Usare il controllo della versione di Team Foundation).|
|[Usare l'automazione dell'interfaccia utente per testare il codice](/visualstudio/test/use-ui-automation-to-test-your-code/)|Solo Windows|La registrazione dei test dell'interfaccia utente di Visual Studio è disponibile solo per Windows. Per tutte le piattaforme, vedere [Xamarin.UITest](/appcenter/test-cloud/uitest/).|

## <a name="improve-code-quality"></a>Migliorare la qualità del codice

Collegamento di riferimento: **[Migliorare la qualità del codice](/visualstudio/test/improve-code-quality)**

|Caratteristica|Supportato con Xamarin|Commenti aggiuntivi|
|-------------|----------------------------|-------------------------|
|[Analizzare la qualità del codice gestito](/visualstudio/code-quality/analyzing-managed-code-quality-by-using-code-analysis)|Sì||
|[Ricerca del codice duplicato mediante il rilevamento del clone di codice](https://msdn.microsoft.com/library/hh205279.aspx)|Sì||
|[Misurazione della complessità e della manutenibilità del codice gestito](/visualstudio/code-quality/measuring-complexity-and-maintainability-of-managed-code)|Sì||
|[Performance Explorer](/visualstudio/profiling/performance-explorer) (Esplora prestazioni)|No|In alternativa, usare la [Xamarin Profiler](/xamarin/tools/profiler/) tramite Visual Studio per Mac. Si noti che il Profiler Xamarin è attualmente in versione di anteprima e non funziona ancora per le destinazioni Windows.|
|[Analizzare i problemi relativi alla memoria .NET Framework](https://msdn.microsoft.com/library/dn342825.aspx)|No|Gli strumenti di Visual Studio non hanno hook nel framework Mono per la profilatura.|

## <a name="release-management"></a>Gestione versioni

Collegamento di riferimento: **[Build and Release in Azure Pipelines and TFS](/azure/devops/pipelines/overview?view=vsts)** (Compilazione e rilascio in Azure Pipelines e TFS)

|Caratteristica|Supportato con Xamarin|Commenti aggiuntivi|
|-------------|----------------------------|-------------------------|
|Gestire i processi di rilascio|Sì||
|Distribuzione ai server per il caricamento laterale tramite script|Sì||
|Caricare nell'app store|Parziale|Sono disponibili estensioni che possono automatizzare questo processo per alcuni archivi applicazioni.  Vedere le [estensioni per Azure DevOps Services](https://marketplace.visualstudio.com/VSTS), ad esempio l'[estensione per Google Play](https://marketplace.visualstudio.com/items?itemName=ms-vsclient.google-play).|

## <a name="monitor-with-hockeyapp"></a>Monitorare con HockeyApp

Collegamento di riferimento: **[Monitorare con HockeyApp](https://www.hockeyapp.net/features/)**

|Caratteristica|Supportato con Xamarin|Commenti aggiuntivi|
|-------------|----------------------------|-------------------------|
|Analisi degli arresti anomali, telemetria e distribuzione beta|Sì||
