---
title: tvOS Cenni preliminari sulla distribuzione di App
description: Questo documento viene fornita una panoramica delle tecniche di distribuzione disponibili per app Xamarin.tvOS e funge da un puntatore a documenti più dettagliati per l'argomento.
ms.prod: xamarin
ms.assetid: D5E0F446-C083-4E21-9788-FC84D32D00C4
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/16/2017
ms.openlocfilehash: 440d980c39434165ba9a59ccf67850977c8f75c3
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/05/2018
ms.locfileid: "34788745"
---
# <a name="tvos-app-distribution-overview"></a>tvOS Cenni preliminari sulla distribuzione di App

_Questo documento viene fornita una panoramica delle tecniche di distribuzione disponibili per app Xamarin.tvOS e funge da un puntatore a documenti più dettagliati per l'argomento._


Una volta sviluppato l'app Xamarin.tvOS, è il passaggio successivo nel ciclo di vita di sviluppo software per distribuire l'app agli utenti, come illustrato nella sezione evidenziata del diagramma riportato di seguito:


[![Panoramica di ciclo di vita dello sviluppo di software](images/publishingdiagram.png)](images/publishingdiagram.png#lightbox)


Apple fornisce le seguenti per distribuire un'app tvOS, supportati da Xamarin.tvOS:

1. [**App Store**](#Apple-TV-App-Store-Distribution)
2. [**In-House (Enterprise)**](#In-House-Distribution) 
2. [**Ad hoc**](#Ad_Hoc_Distribution) 

In tutti questi scenari è necessario effettuare il provisioning delle applicazioni usando il *profilo di provisioning* appropriato. I profili di provisioning sono file che contengono le informazioni sulla firma del codice, oltre all'identità dell'applicazione e al meccanismo di distribuzione previsto. Per la distribuzione senza App Store contengono anche informazioni sui dispositivi in cui l'app può essere distribuita.

<a name="Apple-TV-App-Store-Distribution" />

## <a name="apple-tv-app-store-distribution"></a>Distribuzione di App Store di Apple TV

Si tratta della modalità principale che tvOS App vengano distribuite a utenti nei dispositivi di Apple TV. Tutte le app inviate all'App Store di Apple TV richiedono l'approvazione da Apple.

Le app vengono inviate all'App Store tramite un portale chiamato *iTunes Connect*. Consultare il nostro [configurata l'applicazione in iTunes Connect](~/ios/deploy-test/app-distribution/app-store-distribution/itunesconnect.md) guide per informazioni sugli argomenti seguenti:

- La gestione dei contratti, imposte e servizi bancari.
- Creazione di un iTunes Connect Record.
- Gestione App video e schermate.
- La gestione di App nome, descrizione, quali sono le novità, le parole chiave e gli URL.
- Gestione delle informazioni applicazione generale.
- Gestione delle informazioni Game Center.
- Gestione delle informazioni di revisione di App.
- Gestione di informazioni sui prezzi.
- Gestione delle informazioni di acquisto In-App.
- Visualizzazione delle revisioni di applicazione.

Sebbene non scritto in modo specifico per tvOS, questo documento vengono fornite informazioni su come configurare e usare questo portale per preparare l'app per la pubblicazione in App Store di Apple TV. Molti degli stessi passaggi sono necessari se si configura un'app iOS o tvOS.

Dopo aver completato tutti i passaggi elencati in precedenza, vedere il nostro [configurare l'App tvOS in iTunes Connect](~/ios/tvos/deploy-test/app-distribution/itunes-connect.md) tvOS informazioni dell'applicazione specifica che verrà richiesto di aggiungere.

È importante tenere presente che solo gli sviluppatori che appartengono al **programma Apple Developer** hanno accesso a iTunes Connect. I membri del **programma Apple Developer Enterprise** non hanno accesso.

Se si sono verificati problemi di invio dell'app Xamarin.tvOS per App Store di Apple TV, consultare il nostro [Troubleshooting](~/ios/tvos/troubleshooting.md) Guida. Contiene diversi problemi che potrebbero verificarsi e come risolverli nel Xamarin.tvOS.

Per ulteriori informazioni, visitare il [la pubblicazione in App Store di Apple TV](~/ios/tvos/deploy-test/app-distribution/app-store-publishing.md) Guida.

<a name="In-House-Distribution" />

## <a name="in-house-distribution"></a>Distribuzione interna

La distribuzione interna, chiamata a volte *distribuzione aziendale*, consente a tutti i membri del **programma Apple Developer Enterprise** di distribuire le app internamente ad altri membri della stessa organizzazione. La distribuzione interna presenta il vantaggio di non dover esaminare l'App Store e di poter installare un'applicazione in un numero illimitato di dispositivi. È tuttavia importante tenere presente che i membri del **programma Apple Developer Enterprise** **non** hanno accesso a iTunes Connect e quindi il licenziatario è responsabile della distribuzione dell'app.

Per ulteriori informazioni sulla configurazione e su come distribuire l'applicazione internamente, consultare il [Guida alla distribuzione interne](~/ios/deploy-test/app-distribution/in-house-distribution.md). Questo documento è specifico di iOS, ma vengono utilizzate le stesse tecniche per tvOS app.

<a name="Ad_Hoc_Distribution"/>

## <a name="ad-hoc-distribution"></a>Distribuzione ad hoc

App Xamarin.tvOS può essere sottoposto a test utente tramite la distribuzione di ad hoc, cui è disponibile in entrambi i **programma per sviluppatori Apple**e **Apple Developer Enterprise Program**e consente ai dispositivi Apple TV fino a 100 da testare. Migliore dei casi di utilizzo per la distribuzione ad hoc è distribuzione all'interno della società quando iTunes Connect non è un'opzione.

Per ulteriori informazioni sulla configurazione e su come distribuire l'app internamente, consultare il [Guida alla distribuzione Ad Hoc](~/ios/deploy-test/app-distribution/ad-hoc-distribution.md). Anche in questo documento è specifico di iOS, ma vengono utilizzate le stesse tecniche per tvOS app.

<a name="Summary" />

## <a name="summary"></a>Riepilogo

In questo articolo ha fornito una breve panoramica dei meccanismi di distribuzione che sono disponibili per App Xamarin.tvOS. Sono stati introdotti App Store di Apple TV, Ad Hoc e internamente la distribuzione e forniti i collegamenti a informazioni più dettagliate.



## <a name="related-links"></a>Collegamenti correlati

- [Esempi di tvOS](https://developer.xamarin.com/samples/tvos/all/)
- [tvOS](https://developer.apple.com/tvos/)
- [tvOS Guide interfaccia umana](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Guida alla programmazione di App per tvOS](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
