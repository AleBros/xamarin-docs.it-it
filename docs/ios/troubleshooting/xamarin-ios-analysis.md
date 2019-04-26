---
title: Regole di analisi di xamarin. IOS
description: Questo documento descrive un set di regole di analisi che consentono di controllare le impostazioni di progetto xamarin. IOS per consentire di determinare se sono disponibili impostazioni ancora/better-optimized.
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: C29B69F5-08E4-4DCC-831E-7FD692AB0886
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/06/2018
ms.openlocfilehash: 8a4990ce7b2bcacbd4b97b214458531b3d94122e
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61416383"
---
# <a name="xamarinios-analysis-rules"></a>Regole di analisi di xamarin. IOS

Analisi di xamarin. IOS è un set di regole che consentono di controllare le impostazioni del progetto per determinare se sono disponibili le impostazioni ottimizzate migliore o più.

Eseguire le regole di analisi più frequentemente possibile per individuare possibili miglioramenti da apportare all'inizio e di risparmiare tempo di sviluppo.

Per eseguire le regole, in Visual Studio per il menu del Mac, selezionare **progetto > Esegui analisi del codice**.

> [!NOTE]
> Analisi di xamarin. IOS viene eseguito solo informazioni sulla configurazione attualmente selezionata. È consigliabile eseguire lo strumento per il debug **e** configurazioni di rilascio.

<a name="XIA0001" />

## <a name="xia0001-disabledlinkerrule"></a>XIA0001: DisabledLinkerRule

- **Problema:** Il linker è disabilitato nel dispositivo per la modalità di debug.
- **Fix:** È consigliabile eseguire il codice con il linker per evitare qualsiasi sorpresa.
Per configurarlo, passare al progetto > compilazione iOS > comportamento del Linker.

<a name="XIA0002" />

## <a name="xia0002-testcloudagentreleaserule"></a>XIA0002: TestCloudAgentReleaseRule

- **Problema:** Build di App che consentono di inizializzare l'agente di Test Cloud verranno rifiutate da Apple quando inviato, perché usano API private.
- **Fix:** Aggiungere o correggere le necessarie #if e definisce nel codice.

<a name="XIA0003" />

## <a name="xia0003-ipadebugbuildsrule"></a>XIA0003: IPADebugBuildsRule

- **Problema:** Configurazione di debug che usa le chiavi di firma per gli sviluppatori non deve generare un file IPA quando è necessario solo per la distribuzione, che ora Usa la pubblicazione guidata.
- **Fix:** Disabilitare la compilazione di file IPA in Opzioni progetto per la configurazione di Debug.

<a name="XIA0004" />

## <a name="xia0004-missing64bitsupportrule"></a>XIA0004: Missing64BitSupportRule

- **Problema:** L'architettura supportata per la "versione | dispositivo"non è compatibile, mancante ARM64 a 64 bit. Ciò costituisce un problema come Apple non accetta 32 bit solo app di iOS in App Store.
- **Fix:** Fare doppio clic sul progetto iOS, passare alla compilazione > iOS creare e modificare le architetture supportate in modo che abbia ARM64.

<a name="XIA0005" />

## <a name="xia0005-float32rule"></a>XIA0005: Float32Rule

- **Problema:** Non si utilizza l'opzione float32 (-aot-Opzioni-= O = float32) assicura prestazioni notevole dei costi, specialmente su dispositivi mobili, in cui è più lento misurabile matematica a precisione doppia. Si noti che .NET usa a precisione doppia internamente, anche per float, in modo che l'abilitazione di questa opzione influisce sulla precisione e, possibilmente, compatibilità.
- **Fix:** Fare doppio clic sul progetto iOS, passare alla compilazione > iOS Build e deselezionare l'opzione di "Eseguire tutte le operazioni a virgola mobile a 32 come valore float a 64 bit".

<a name="XIA0006" />

## <a name="xia0006-httpclientavoidmanaged"></a>XIA0006: HttpClientAvoidManaged

- **Problema:** È consigliabile usare il gestore di HttpClient nativo anziché in quello gestito per ottenere prestazioni migliori, dimensioni dei file eseguibili più piccole e per supportare facilmente gli standard più recenti.
- **Fix:** Fare doppio clic sul progetto iOS, passare alla compilazione > iOS creare e modificare l'implementazione di HttpClient NSUrlSession (iOS 7 +) o CFNetwork per supportare una versione precedente di iOS 7.
