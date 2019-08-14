---
title: Regole di analisi di Novell. iOS
description: Questo documento descrive un set di regole di analisi che controllano le impostazioni del progetto Novell. iOS per determinare se sono disponibili impostazioni più o ottimizzate in modo più efficace.
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: C29B69F5-08E4-4DCC-831E-7FD692AB0886
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/06/2018
ms.openlocfilehash: 5f968e01cc0b866f94f524728b4bba1e759e8bf8
ms.sourcegitcommit: 9f37dc00c2adab958025ad1cdba9c37f0acbccd0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/14/2019
ms.locfileid: "69012389"
---
# <a name="xamarinios-analysis-rules"></a>Regole di analisi di Novell. iOS

L'analisi di Novell. iOS è un set di regole che controllano le impostazioni del progetto per determinare se sono disponibili impostazioni ottimizzate o migliorate.

Eseguire le regole di analisi il più spesso possibile per individuare i possibili miglioramenti in anticipo e risparmiare tempo di sviluppo.

Per eseguire le regole, nel menu di Visual Studio per Mac selezionare **progetto > Esegui analisi codice**.

> [!NOTE]
> L'analisi di Novell. iOS viene eseguita solo sulla configurazione attualmente selezionata. Si consiglia vivamente di eseguire lo strumento per le configurazioni di debug **e** di rilascio.

<a name="XIA0001" />

## <a name="xia0001-disabledlinkerrule"></a>XIA0001: DisabledLinkerRule

- **Problema:** Il linker è disabilitato nel dispositivo per la modalità di debug.
- **Difficoltà** È consigliabile provare a eseguire il codice con il linker per evitare eventuali sorprese.
Per configurarlo, passare a Project > iOS Build > il comportamento del linker.

<a name="XIA0002" />

## <a name="xia0002-testcloudagentreleaserule"></a>XIA0002: TestCloudAgentReleaseRule

- **Problema:** Le compilazioni di app che inizializzano l'agente di Test Cloud verranno rifiutate da Apple quando vengono inviate, perché usano l'API privata.
- **Difficoltà** Aggiungere o correggere le #if e le definizioni necessarie nel codice.

<a name="XIA0003" />

## <a name="xia0003-ipadebugbuildsrule"></a>XIA0003: IPADebugBuildsRule

- **Problema:** La configurazione di debug che usa le chiavi di firma dello sviluppatore non deve generare un IPA perché è necessario solo per la distribuzione, che ora usa la pubblicazione guidata.
- **Difficoltà** Disabilitare la compilazione IPA nelle opzioni del progetto per la configurazione di debug.

<a name="XIA0004" />

## <a name="xia0004-missing64bitsupportrule"></a>XIA0004: Missing64BitSupportRule

- **Problema:** Architettura supportata per "versione | il dispositivo "non è compatibile con 64 bit, manca ARM64. Si tratta di un problema perché Apple non accetta solo app iOS a 32 bit nell'AppStore.
- **Difficoltà** Fare doppio clic sul progetto iOS, passare a compilare > iOS compilare e modificare le architetture supportate in modo che disponga di ARM64.

<a name="XIA0005" />

## <a name="xia0005-float32rule"></a>XIA0005: Float32Rule

- **Problema:** Se non si usa l'opzione float32 (--AOT-Options =-O = float32), si ottengono costi elevati in termini di prestazioni, soprattutto per dispositivi mobili, in cui la matematica a precisione doppia è significativamente più lenta. Si noti che .NET usa la precisione doppia internamente, anche per float, quindi l'abilitazione di questa opzione influiscono sulla precisione e, possibilmente, sulla compatibilità.
- **Difficoltà** Fare doppio clic sul progetto iOS, passare a Compila > compilazione iOS e deselezionare l'opzione "Esegui tutte le operazioni float a 32 bit come float a 64 bit".

<a name="XIA0006" />

## <a name="xia0006-httpclientavoidmanaged"></a>XIA0006: HttpClientAvoidManaged

- **Problema:** È consigliabile utilizzare il gestore HttpClient nativo anziché quello gestito per ottenere prestazioni migliori, dimensioni eseguibili più piccole e supportare facilmente gli standard più recenti.
- **Difficoltà** Fare doppio clic sul progetto iOS, passare a Compila > compilare iOS e modificare l'implementazione di HttpClient in NSUrlSession (iOS 7 +) o CFNetwork per supportare la versione precedente iOS 7.

<a name="XIA0007" />

## <a name="xia0007-usellvmrule"></a>XIA0007: UseLLVMRule

- **Problema:** Per la versione | Configurazione iPhone è consigliabile abilitare il compilatore LLVM che genera codice più veloce da eseguire a scapito del tempo di compilazione.
- **Difficoltà** Fare doppio clic sul progetto iOS, passare a Compila > compilazione iOS e per la versione | iPhone, controllare l'opzione del compilatore LLVM Optimizing.
