---
title: Regole di analisi di xamarin
ms.topic: article
ms.prod: xamarin
ms.assetid: C29B69F5-08E4-4DCC-831E-7FD692AB0886
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 06/26/2017
ms.openlocfilehash: 7cf627f369b666bb54d0f512dc1361d2a685a057
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2018
---
# <a name="xamarinios-analysis-rules"></a>Regole di analisi di xamarin


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

- **Problema:** non utilizza l'opzione float32 (-aot-Opzioni-O = = float32) in modo hefty sulle prestazioni, in modo speciale in dispositivi mobili, dove double matematiche di precisione è misurabile più lento. Si noti che .NET utilizza precisione doppia internamente, anche per float, in modo da abilitare questa opzione influisce sulla precisione e, possibilmente, compatibilità.
- **Correzione:** Double fare clic sul progetto iOS, passare alla compilazione > iOS compilare e deselezionare il "Come valore float a 64 bit, eseguire tutte le operazioni float a 32 bit".
