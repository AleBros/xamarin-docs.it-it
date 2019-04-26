---
title: È possibile usare una versione precedente di Xcode o xamarin. IOS
description: Questa guida descrive i problemi con le versioni precedenti di Xcode o xamarin. IOS (rispetto alla versione stabile corrente).
ms.prod: xamarin
ms.assetid: 27CF7EB7-9251-435F-BEA5-F20F8DD7DC17
ms.technology: xamarin-ios
author: chamons
ms.author: chhamo
ms.date: 04/16/2019
ms.openlocfilehash: 2a208d39454a33adc849bcccc66802361693e82e
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61419315"
---
# <a name="can-i-use-an-older-version-of-xcode-or-xamarinios"></a>È possibile usare una versione precedente di Xcode o xamarin. IOS?

Documentazione di Xamarin presuppone l'uso di xamarin. IOS più recenti e Xcode, che è consigliata. Tuttavia, alcuni clienti preferiscono usare meno recenti xamarin. IOS e/o Xcode e vogliono ricevere informazioni sulle conseguenze.

Le note sulla versione contengono l'avviso seguente:

> [!WARNING]
> **Usa una versione precedente di Xcode**
>
> Usa una versione precedente di Xcode (rispetto a quello indicato nell'esempio precedente [requisiti](https://docs.microsoft.com/xamarin/ios/release-notes/12/12.8#requirements)) è spesso possibile, ma alcune funzionalità potrebbero non essere disponibili. Inoltre alcune limitazioni potrebbero richiedere soluzioni alternative, ad esempio:
>
> - Il programma di registrazione statico richiede i file di intestazioni Xcode per creare applicazioni, causando [ `MT0091` ](https://docs.microsoft.com/xamarin/ios/troubleshooting/mtouch-errors#MT0091) oppure [ `MT4109` ](https://docs.microsoft.com/xamarin/ios/troubleshooting/mtouch-errors#MT4109) errori se non sono disponibili le API. Nella maggior parte dei casi l'abilitazione del linker gestito consentirà (tramite la rimozione dell'API).
> - Le compilazioni Bitcode (per tvOS e watchOS) possono non riuscire per l'App Store submission a meno che non viene utilizzata una toolchain di Xcode 9.0 +.

## <a name="further-information"></a>Per ulteriori informazioni

Microsoft consiglia l'utilizzo di Xcode più recenti e una versione più recente di xamarin. IOS per lo sviluppo e invio di applicazioni. Apple richiede l'utilizzo più recente di Xcode durante l'invio di applicazioni.

Si noti che usando più recente di Xcode non impedisce l'applicazione da destinate a versioni precedenti di iOS. Si basa le versioni di iOS è supportare le **Info. plist** movimento e le API Usa l'applicazione.

È possibile installare più versioni di Xcode side-by-side, con nomi diversi, ad esempio **Xcode101.app** e **Xcode102.app**. Se si usano più versioni, assicurarsi di impostare Xcode attivo [di Visual Studio per Mac impostazioni](~/ios/troubleshooting/questions/ios-sdk.md) e con la [ `xcode-select` ](https://developer.apple.com/library/archive/technotes/tn2339/_index.html#//apple_ref/doc/uid/DTS40014588-CH1-HOW_DO_I_SELECT_THE_DEFAULT_VERSION_OF_XCODE_TO_USE_FOR_MY_COMMAND_LINE_TOOLS_) [strumento da riga di comando](https://developer.apple.com/library/archive/technotes/tn2339/_index.html#//apple_ref/doc/uid/DTS40014588-CH1-HOW_DO_I_SELECT_THE_DEFAULT_VERSION_OF_XCODE_TO_USE_FOR_MY_COMMAND_LINE_TOOLS_).

Tuttavia, casi rari possono richiedere l'uso dei componenti meno recenti. Questa documentazione descrive le sfide generali che possono verificarsi quando con le versioni precedenti alla più recente.

Ogni versione di Apple è univoco, tuttavia, e possono incontrare altri problemi non documentati qui.

Questi problemi sono talvolta non è semplice da risolvere, in modo che quando possibile mantenere la configurazione supportata più recente Xcode e xamarin. IOS più recente.

## <a name="use-of-an-old-xamarinios-with-an-old-xcode"></a>Uso di una vecchia xamarin. IOS con Xcode un vecchio

Aggiornamento di Xcode e xamarin. IOS non è possibile, almeno per un determinato intervallo di tempo. Il limite è che, a un certo punto, Apple richiede almeno la versione di Xcode per inviare le applicazioni. A questo punto è necessario aggiornare tutti i componenti (Mac OS, Xcode e xamarin. IOS) per le versioni più recenti (o la versione nuova, minima di Xcode richiesti da Apple e la versione di xamarin. IOS corrispondente).

È in genere più semplice aggiornare gradualmente e allinearsi con piccole modifiche. Per progetti di grandi dimensioni in cui gli aggiornamenti possono essere più difficile stare al passo con rimanere con set noto di utilizzo può essere un buon compromesso.

## <a name="use-of-new-xamarinios-with-older-xcode"></a>Utilizzo del nuovo xamarin. IOS con Xcode precedente

In generale, xamarin. IOS supporta versioni precedenti di Xcode, ogni volta che è ragionevolmente possibile. Alcuni potenziali sfide da affrontare includono:

- Di xamarin. IOS più recenti potrebbero supportare alcune funzionalità e le API presentano in Xcode selezionato. 
- Il **registrar statici** richiede i file di intestazioni di Xcode per creare applicazioni, causando [ `MT0091` ](~/ios/troubleshooting/mtouch-errors.md#MT0091) oppure [ `MT4109` ](~/ios/troubleshooting/mtouch-errors.md#MT4109)' errori se non sono disponibili le API.
  - Nella maggior parte dei casi l'abilitazione del linker gestito aiuterà (rimuovendo le associazioni gestite per la nuova API) se non utilizzato.
- Le compilazioni Bitcode (per tvOS e watchOS) possono non riuscire per l'App Store submission a meno che non viene utilizzata una toolchain di Xcode 9.0 +.

## <a name="use-of-new-xcode-with-older-xamarinios"></a>Uso di Xcode nuovi con xamarin. IOS precedenti

In questo caso d'uso è molto più difficile, perché xamarin. IOS non è in grado di stimare i requisiti di modifica del nuovo Xcode. Gli aggiornamenti di macOS possono causare problemi e senza patch compatibilità può esserne influenzate molte parti di xamarin. IOS. 

Esistono una serie di potenziali aree possono esiti tra cui:

- Eventuali incompatibilità con `mlaunch`:
  - Supporto di simulatore potrebbe non funzionare correttamente (o tutti)
  - Supporto dei dispositivi potrebbe non funzionare correttamente (o tutti)
- Supporto sconosciuto per `mtouch` 
  - Nessun supporto per nuovi Framework
  - Nessun supporto per nuovi diritti
  - Nessun supporto per strumenti nuovi o aggiornati
    - Questo può influire anche di firma del codice

### <a name="new-appstore-submission-rules"></a>Nuove regole di invio dall'App Store

Apple riserva il diritto di aggiornamenti per le regole di App Store gli invii in qualsiasi momento. Queste regole modificate solo in alcuni casi sono annunciate in anticipo. Alcune di queste modifiche richiedono strumenti modifiche per il supporto, che richiederebbero un componente di xamarin. IOS aggiornato.

Oltre a modificare la regola, Apple spesso aggiunge le convalide aggiuntive all'App inviate o migliorare quelle esistenti. Alcuni di questi richiedono modifiche in questi strumenti (ad esempio, un nuovo disattivate simboli). Molte di queste vengono innanzitutto verificati dai clienti invio, nessun annuncio (o elenco) delle regole.

## <a name="summary"></a>Riepilogo

Quando possibile, correre rischi di Apple seguenti linee guida e lo sviluppo su e inviando con più recente di Xcode ha rilasciato l'App Store.

Avvalersi di xamarin. IOS più recenti rilasciati. Si monitoreranno le correzioni più recenti che potrebbero influire sulle applicazioni inviate e rispettare le modifiche alle regole più recente.

Se non è fattibile, valutare l'uso di una corrispondente versione di Xcode e xamarin. IOS meno recente. Questo approccio può funzionare per un periodo di tempo, ma in futuro verrà insistere Apple al momento gli strumenti più recenti, quindi pianificare di conseguenza.
