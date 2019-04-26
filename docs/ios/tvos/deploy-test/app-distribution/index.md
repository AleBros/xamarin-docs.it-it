---
title: Panoramica della distribuzione di App tvOS
description: Questo documento offre una panoramica delle tecniche di distribuzione disponibili per app xamarin. tvos e funge da un puntatore a documenti più dettagliati sull'argomento.
ms.prod: xamarin
ms.assetid: D5E0F446-C083-4E21-9788-FC84D32D00C4
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/16/2017
ms.openlocfilehash: 0a40d50d02008439e81d5db19bcda0647203e2da
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61414425"
---
# <a name="tvos-app-distribution-overview"></a>Panoramica della distribuzione di App tvOS

_Questo documento offre una panoramica delle tecniche di distribuzione disponibili per app xamarin. tvos e funge da un puntatore a documenti più dettagliati sull'argomento._


Una volta all'app xamarin. tvos è stata sviluppata, il passaggio successivo del ciclo di vita di sviluppo software è distribuire l'app agli utenti, come illustrato nella sezione evidenziata del diagramma seguente:


[![La panoramica del ciclo di vita dello sviluppo di software](images/publishingdiagram.png)](images/publishingdiagram.png#lightbox)


Apple offre modi seguenti per distribuire un'app tvOS, che sono supportati da xamarin. tvos:

1. [**App Store**](#Apple-TV-App-Store-Distribution)
2. [**In-House (Enterprise)**](#In-House-Distribution) 
2. [**Ad hoc**](#Ad_Hoc_Distribution) 

In tutti questi scenari è necessario effettuare il provisioning delle applicazioni usando il *profilo di provisioning* appropriato. I profili di provisioning sono file che contengono le informazioni sulla firma del codice, oltre all'identità dell'applicazione e al meccanismo di distribuzione previsto. Per la distribuzione senza App Store contengono anche informazioni sui dispositivi in cui l'app può essere distribuita.

<a name="Apple-TV-App-Store-Distribution" />

## <a name="apple-tv-app-store-distribution"></a>Distribuzione di Apple TV App Store

Questo è il modo principale che le app tvOS vengono distribuite agli utenti nei dispositivi Apple TV. Tutte le app inviate alla Store di App Apple TV richiedono l'approvazione di Apple.

Le app vengono inviate all'App Store tramite un portale chiamato *iTunes Connect*. Vedere la [configurare l'App in iTunes Connect](~/ios/deploy-test/app-distribution/app-store-distribution/itunesconnect.md) Guida per informazioni sugli argomenti seguenti:

- La gestione dei contratti, imposte e banche.
- Creazione un Record iTunes Connect.
- La gestione delle App video e screenshot.
- Gestione di App nome, descrizione, novità, parole chiave e URL.
- Gestione di informazioni generali sull'App.
- Gestione delle informazioni di Game Center.
- Gestione delle informazioni di revisione dell'App.
- Gestione di informazioni sui prezzi.
- Gestione delle informazioni sull'acquisto In-App.
- Visualizzazione delle revisioni dell'applicazione.

Anche se non è scritto specificamente per tvOS, questo documento vengono fornite informazioni su come configurare e usare questo portale per preparare l'app per la pubblicazione nel Store di App Apple TV. Molti dei passaggi sono necessari se si configura un'app iOS o tvOS.

Dopo aver completato tutti i passaggi elencati in precedenza, vedere la [configurare l'App tvOS in iTunes Connect](~/ios/tvos/deploy-test/app-distribution/itunes-connect.md) per aggiungere le informazioni specifiche di app tvOS che verrà richiesto.

È importante tenere presente che solo gli sviluppatori che appartengono al **programma Apple Developer** hanno accesso a iTunes Connect. I membri del **programma Apple Developer Enterprise** non hanno accesso.

Se si sono verificati problemi di invio dell'app xamarin. tvos ad la Store di App Apple TV, vedere la [Troubleshooting](~/ios/tvos/troubleshooting.md) Guida. Contiene alcuni problemi noti che possono verificarsi e come risolverli in di xamarin. tvos.

Per altre informazioni, visitare il [pubblicazione di Store di App Apple TV](~/ios/tvos/deploy-test/app-distribution/app-store-publishing.md) Guida.

<a name="In-House-Distribution" />

## <a name="in-house-distribution"></a>Distribuzione interna

La distribuzione interna, chiamata a volte *distribuzione aziendale*, consente a tutti i membri del **programma Apple Developer Enterprise** di distribuire le app internamente ad altri membri della stessa organizzazione. La distribuzione interna presenta il vantaggio di non dover esaminare l'App Store e di poter installare un'applicazione in un numero illimitato di dispositivi. È tuttavia importante tenere presente che i membri del **programma Apple Developer Enterprise** **non** hanno accesso a iTunes Connect e quindi il licenziatario è responsabile della distribuzione dell'app.

Per altre informazioni sulla configurazione e come distribuire l'applicazione internamente, consultare il [Guida distribuzione interna](~/ios/deploy-test/app-distribution/in-house-distribution.md). Questo documento è specifico di iOS, ma vengono utilizzate le stesse tecniche per le app tvOS.

<a name="Ad_Hoc_Distribution"/>

## <a name="ad-hoc-distribution"></a>Distribuzione ad hoc

Le app xamarin. tvos possono essere testate dagli utenti tramite la distribuzione ad hoc, cui è disponibile in entrambe le **Apple Developer Program**e il **Apple Developer Enterprise Program**che consente fino a 100 dispositivi Apple TV da sottoporre a test. Il caso d'uso ideale per la distribuzione ad hoc è distribuzione all'interno della società quando iTunes Connect non è un'opzione.

Per altre informazioni sulla configurazione e come distribuire l'app internamente, consultare il [Guida alla distribuzione Ad Hoc](~/ios/deploy-test/app-distribution/ad-hoc-distribution.md). Anche in questo documento è specifico di iOS, ma vengono utilizzate le stesse tecniche per le app tvOS.

<a name="Summary" />

## <a name="summary"></a>Riepilogo

Questo articolo ha offerto una breve panoramica dei meccanismi di distribuzione disponibili per le app xamarin. tvos. Ha introdotto la Store di App Apple TV, Ad Hoc e internamente la distribuzione e fornisce collegamenti a informazioni più dettagliate.



## <a name="related-links"></a>Collegamenti correlati

- [Esempi di tvOS](https://developer.xamarin.com/samples/tvos/all/)
- [tvOS](https://developer.apple.com/tvos/)
- [le guide dell'interfaccia umana tvOS](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Guida alla programmazione di App per tvOS](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
