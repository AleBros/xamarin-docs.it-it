---
title: Test e distribuzione di Xamarin.iOS - Risoluzione dei problemi
description: Questo documento fornisce suggerimenti sulla risoluzione dei problemi relativi alla firma del codice e al provisioning, a TestFlight e alla copia del bundle dell'app iOS dall'host di compilazione Mac a Windows.
ms.prod: xamarin
ms.assetid: 65286D09-F74D-4F22-B6CD-D1BCD7FC7992
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 08/23/2017
ms.openlocfilehash: a290f29707bd59a22f612f31e544a211488eba0d
ms.sourcegitcommit: 1dd7d09b60fcb1bf15ba54831ed3dd46aa5240cb
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70121490"
---
# <a name="xamarinios-testing-and-deployment---troubleshooting"></a>Test e distribuzione di Xamarin.iOS - Risoluzione dei problemi

## <a name="code-signing--provisioning"></a>Firma del codice e provisioning

Le operazioni di firma del codice e provisioning con iOS possono essere complesse, quindi è importante verificare che i certificati di firma del codice e i profili di provisioning siano in ordine.

- I team di grandi dimensioni dovrebbero evitare di usare il pulsante "Fix issue" (Correggi problema), illustrato di seguito:

    [![](troubleshooting-images/fixissue.png "Pulsante Fix issue (Correggi problema)")](troubleshooting-images/fixissue.png#lightbox)

    In questo modo vengono infatti creati nuovi profili di provisioning e certificati. Nella migliore delle ipotesi, viene creato un profilo di provisioning ogni volta che un membro del team fa clic sul pulsante, causando una disorganizzazione dei profili. Nella peggiore, vengono revocati i certificati per tutte le altre persone in azienda, le cui app smetteranno quindi di funzionare.

- Mantenere organizzato l'accesso keychain ed eliminare i certificati e i profili scaduti. I certificati Enterprise hanno una validità di tre anni, mentre gli altri di un anno. I certificati non possono essere rinnovati, quindi è necessario crearne di nuovi prima che quelli vecchi scadano. Assicurarsi di revocare ed eliminare i vecchi certificati e di firmare di nuovo le app con i nuovi certificati.

- Rimuovere i vecchi profili di provisioning appena si installano quelli nuovi. Questo significa che Visual Studio per Mac non è in una posizione in cui deve decidere quale profilo usare. Per ottenere questo risultato, eliminare prima di tutto il profilo nel centro sviluppatori Apple, quindi passare a *Preferenze > Account > Visualizza dettagli*. Selezionare il profilo di provisioning e fare clic su **Mostra nel Finder**. Verrà visualizzata la posizione del profilo nel file system Mac, da cui potrà quindi essere eliminato mediante il Finder.

- Assicurarsi che siano disponibili tutti i certificati necessari e le corrispondenti chiavi private. Per ogni team sarà necessario un certificato dello sviluppatore (per installare le app in un dispositivo personale) e un certificato di distribuzione (per installarle in altri dispositivi).

- Riavviare Xcode e Visual Studio per Mac/Visual Studio quando viene installato un nuovo profilo di provisioning o un nuovo certificato.

## <a name="testflight"></a>TestFlight

A volte il testing non procede senza intoppi come si era pianificato.  Le indicazioni seguenti possono essere utili per risolvere i problemi con TestFlight:

- TestFlight è disponibile solo per le app per iOS 8 e versioni successive.

- Deve essere presente un *profilo di distribuzione tramite App Store* con l'entitlement beta.

- La finestra **New iOS App submission** (Invio nuova app iOS) deve contenere esattamente le stesse informazioni specificate nel file **Info.plist** dell'app e tutte le sezioni devono essere compilate. È necessario specificare le icone per l'app prima del caricamento in TestFlight.

- Quando si carica una nuova build, occorrono da 1 a 5 minuti prima che appaia in iTunes Connect.

- L'opzione di [test della versione beta di TestFlight](~/ios/deploy-test/testflight.md#beta-testing) deve essere attivata in ogni versione dell'app.

- Ogni membro del team di sviluppo che è anche un tester interno deve attivare l'opzione **Internal Tester** (Tester interno).

- Gli utenti che appartengono o possiedono un altro account iTunes Connect non possono essere tester interni. Possono essere aggiunti solo come tester esterni.

- Gli utenti interni ed esterni vengono aggiunti, selezionati e invitati separatamente. Ogni elenco deve essere gestito separatamente.

- Apple deve approvare ogni build che verrà distribuita ai tester esterni. Se la versione di una build cambia, Apple dovrà eseguire una nuova revisione della versione beta. Se cambia il numero di build, la revisione è facoltativa.

- È necessario aggiungere i metadati alle build che vengono distribuite a tester esterni. Per accedervi, fare clic sul numero di build in **App personali > Versione preliminare**.

- È possibile inviare per la revisione solo due build al giorno. Poiché il cambio di versione impone una revisione, questo significa che i numeri di versione possono essere cambiati due volte al giorno.

<a name="Automatically_copy_app_bundles_back_to_Windows" />

## <a name="automatically-copy-app-bundles-back-to-windows"></a>Copiare automaticamente i bundle delle app nuovamente in Windows

[!include[](~/ios/includes/copy-app-bundle-to-windows.md)]
