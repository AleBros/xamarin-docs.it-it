---
title: Regole di analisi di xamarin
description: Questo documento descrive un set di regole di analisi che consentono di controllare le impostazioni di progetto xamarin. IOS per determinare se sono disponibili impostazioni più/better-optimized.
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: C29B69F5-08E4-4DCC-831E-7FD692AB0886
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/06/2018
ms.openlocfilehash: 25d2936f70981ed239626193ba6e4993f1378108
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/05/2018
ms.locfileid: "34788898"
---
# <a name="xamarinios-analysis-rules"></a>Regole di analisi di xamarin

Analisi di xamarin è un set di regole che consentono di controllare le impostazioni del progetto che consentono di determinare se sono disponibili le impostazioni ottimizzate/più di una situazione migliore.

Eseguire le regole di analisi più spesso possibile per individuare possibili miglioramenti da apportare all'inizio e di risparmiare tempo di sviluppo.

Per eseguire le regole, in Visual Studio per il menu del Mac, selezionare **progetto > Esegui analisi del codice**.

> [!NOTE]
> Analisi di xamarin. IOS viene eseguito solo alla configurazione selezionata. Si consiglia di eseguire lo strumento per il debug **e** configurazioni di rilascio.

<a name="XIA0001" />

## <a name="xia0001-disabledlinkerrule"></a>XIA0001: DisabledLinkerRule

- **Problema:** il linker è disabilitato per la modalità di debug nel dispositivo.
- **Correzione:** è consigliabile provare a eseguire il codice con il linker per evitare eventuali errori.
Per attivare questa impostazione, passare al progetto > iOS compilazione > comportamento del Linker.

<a name="XIA0002" />

## <a name="xia0002-testcloudagentreleaserule"></a>XIA0002: TestCloudAgentReleaseRule

- **Problema:** verranno rifiutate le compilazioni di App che inizializzano l'agente di Test Cloud da Apple, invio, utilizzare API privata.
- **Correzione:** aggiungere o correggere #if necessari e vengono definite in codice.

<a name="XIA0003" />

## <a name="xia0003-ipadebugbuildsrule"></a>XIA0003: IPADebugBuildsRule

- **Problema:** configurazione di Debug che utilizza le chiavi di firma sviluppatore non deve generare un pacchetto IPA perché è necessaria solo per la distribuzione, che ora utilizza la pubblicazione guidata.
- **Correzione:** disabilitare compilazione IPA nelle opzioni di progetto per la configurazione di Debug.

<a name="XIA0004" />

## <a name="xia0004-missing64bitsupportrule"></a>XIA0004: Missing64BitSupportRule

- **Problema:** l'architettura supportata per la "versione | "dispositivo non è compatibile, mancante ARM64 a 64 bit. Si tratta di un problema di Apple non accetta App per iOS in AppStore solo 32 bit.
- **Correzione:** Double fare clic sul progetto iOS, passare alla compilazione > iOS compilare e modificare le architetture supportate ha ARM64.

<a name="XIA0005" />

## <a name="xia0005-float32rule"></a>XIA0005: Float32Rule

- **Problema:** non utilizza l'opzione float32 (-aot-Opzioni-O = = float32) in modo hefty sulle prestazioni, soprattutto nei dispositivi mobili, dove double matematiche di precisione è misurabile più lento. Si noti che .NET utilizza precisione doppia internamente, anche per float, in modo da abilitare questa opzione influisce sulla precisione e, possibilmente, compatibilità.
- **Correzione:** Double fare clic sul progetto iOS, passare alla compilazione > iOS compilare e deselezionare il "Come valore float a 64 bit, eseguire tutte le operazioni float a 32 bit".

<a name="XIA0006" />

## <a name="xia0006-httpclientavoidmanaged"></a>XIA0006: HttpClientAvoidManaged

- **Problema:** è consigliabile utilizzare il gestore HttpClient nativo anziché quello gestito per ottenere prestazioni migliori, dimensioni dei file eseguibili più piccole e per supportare facilmente gli standard più recenti.
- **Correzione:** Double fare clic sul progetto iOS, passare alla compilazione > iOS compilare e modificare l'implementazione di HttpClient NSUrlSession (iOS 7 +) o CFNetwork per supportare una versione precedente di iOS 7.
