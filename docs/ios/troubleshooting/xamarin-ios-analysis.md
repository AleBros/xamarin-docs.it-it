---
title: Regole di analisi di xamarin
ms.topic: article
ms.prod: xamarin
ms.assetid: C29B69F5-08E4-4DCC-831E-7FD692AB0886
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/06/2018
ms.openlocfilehash: c7dc63cbed0dbdc13dfd2d32a0859c0fe7a29196
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/09/2018
---
# <a name="xamarinios-analysis-rules"></a>Regole di analisi di xamarin

Analisi di xamarin è un set di regole che consentono di controllare le impostazioni del progetto che consentono di determinare se sono disponibili le impostazioni ottimizzate/più di una situazione migliore.

Eseguire le regole di analisi più spesso possibile per individuare possibili miglioramenti da apportare all'inizio e di risparmiare tempo di sviluppo.

Per eseguire le regole, in Visual Studio per il menu del Mac, selezionare **progetto > Esegui analisi del codice**.

> [!NOTE]
> Analisi di xamarin. IOS viene eseguito solo alla configurazione selezionata. Si consiglia di eseguire lo strumento per il debug **e** configurazioni di rilascio.

## <a name="a-namexia0001xia0001-disabledlinkerrule"></a><a name="XIA0001"/>XIA0001: DisabledLinkerRule

- **Problema:** il linker è disabilitato per la modalità di debug nel dispositivo.
- **Correzione:** è consigliabile provare a eseguire il codice con il linker per evitare eventuali errori.
Per attivare questa impostazione, passare al progetto > iOS compilazione > comportamento del Linker.

## <a name="a-namexia0002xia0002-testcloudagentreleaserule"></a><a name="XIA0002"/>XIA0002: TestCloudAgentReleaseRule

- **Problema:** verranno rifiutate le compilazioni di App che inizializzano l'agente di Test Cloud da Apple, invio, utilizzare API privata.
- **Correzione:** aggiungere o correggere #if necessari e vengono definite in codice.

## <a name="a-namexia0003xia0003-ipadebugbuildsrule"></a><a name="XIA0003"/>XIA0003: IPADebugBuildsRule

- **Problema:** configurazione di Debug che utilizza le chiavi di firma sviluppatore non deve generare un pacchetto IPA perché è necessaria solo per la distribuzione, che ora utilizza la pubblicazione guidata.
- **Correzione:** disabilitare compilazione IPA nelle opzioni di progetto per la configurazione di Debug.

## <a name="a-namexia0004xia0004-missing64bitsupportrule"></a><a name="XIA0004"/>XIA0004: Missing64BitSupportRule

- **Problema:** l'architettura supportata per la "versione | "dispositivo non è compatibile, mancante ARM64 a 64 bit. Si tratta di un problema di Apple non accetta App per iOS in AppStore solo 32 bit.
- **Correzione:** Double fare clic sul progetto iOS, passare alla compilazione > iOS compilare e modificare le architetture supportate ha ARM64.

## <a name="a-namexia0005xia0005-float32rule"></a><a name="XIA0005"/>XIA0005: Float32Rule

- **Problema:** non utilizza l'opzione float32 (-aot-Opzioni-O = = float32) in modo hefty sulle prestazioni, soprattutto nei dispositivi mobili, dove double matematiche di precisione è misurabile più lento. Si noti che .NET utilizza precisione doppia internamente, anche per float, in modo da abilitare questa opzione influisce sulla precisione e, possibilmente, compatibilità.
- **Correzione:** Double fare clic sul progetto iOS, passare alla compilazione > iOS compilare e deselezionare il "Come valore float a 64 bit, eseguire tutte le operazioni float a 32 bit".

## <a name="a-namexia0006xia0006-httpclientavoidmanaged"></a><a name="XIA0006"/>XIA0006: HttpClientAvoidManaged

- **Problema:** è consigliabile utilizzare il gestore HttpClient nativo anziché quello gestito per ottenere prestazioni migliori, dimensioni dei file eseguibili più piccole e per supportare facilmente gli standard più recenti.
- **Correzione:** Double fare clic sul progetto iOS, passare alla compilazione > iOS compilare e modificare l'implementazione di HttpClient NSUrlSession (iOS 7 +) o CFNetwork per supportare una versione precedente di iOS 7.
