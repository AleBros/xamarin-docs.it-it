---
title: Panoramica della distribuzione di app tvOS
description: Questo documento offre una panoramica delle tecniche di distribuzione disponibili per l'app Novell. tvOS e funge da puntatore a documenti più dettagliati sull'argomento.
ms.prod: xamarin
ms.assetid: D5E0F446-C083-4E21-9788-FC84D32D00C4
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/16/2017
ms.openlocfilehash: b311f3121ea6a58975d41b9690e31a44daa0951e
ms.sourcegitcommit: 93e6358aac2ade44e8b800f066405b8bc8df2510
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/09/2020
ms.locfileid: "84573703"
---
# <a name="tvos-app-distribution-overview"></a>Panoramica della distribuzione di app tvOS

_Questo documento offre una panoramica delle tecniche di distribuzione disponibili per l'app Novell. tvOS e funge da puntatore a documenti più dettagliati sull'argomento._

Dopo che l'app Novell. tvOS è stata sviluppata, il passaggio successivo del ciclo di vita dello sviluppo del software consiste nel distribuire l'app agli utenti, come illustrato nella sezione evidenziata del diagramma seguente:

[![Panoramica del ciclo di vita dello sviluppo software](images/publishingdiagram.png)](images/publishingdiagram.png#lightbox)

Apple fornisce i modi seguenti per distribuire un'app tvOS, supportata da Novell. tvOS:

1. [**App Store**](#Apple-TV-App-Store-Distribution)
2. [**In-House (Enterprise)**](#In-House-Distribution) 
3. [**Ad hoc**](#Ad_Hoc_Distribution) 

In tutti questi scenari è necessario effettuare il provisioning delle applicazioni usando il *profilo di provisioning* appropriato. I profili di provisioning sono file che contengono le informazioni sulla firma del codice, oltre all'identità dell'applicazione e al meccanismo di distribuzione previsto. Per la distribuzione senza App Store contengono anche informazioni sui dispositivi in cui l'app può essere distribuita.

<a name="Apple-TV-App-Store-Distribution"></a>

## <a name="apple-tv-app-store-distribution"></a>Distribuzione dell'app Store di Apple TV

Questo è il modo principale in cui le app tvOS vengono distribuite ai consumer nei dispositivi Apple TV. Tutte le app inviate all'App Store di Apple TV richiedono l'approvazione di Apple.

Le app vengono inviate all'App Store tramite un portale chiamato *iTunes Connect*. Per informazioni sugli argomenti seguenti, vedere la Guida [configurare l'app in iTunes Connect](~/ios/deploy-test/app-distribution/app-store-distribution/itunesconnect.md) :

- Gestione di contratti, imposte e banche.
- Creazione di un record iTunes Connect.
- Gestione di video e screenshot dell'app.
- Gestione di nome dell'app, descrizione, novità, parole chiave e URL.
- Gestione delle informazioni generali sull'app.
- Gestione delle informazioni di Game Center.
- Gestione delle informazioni di revisione dell'app.
- Gestione delle informazioni sui prezzi.
- Gestione delle informazioni di acquisto in-app.
- Visualizzazione delle revisioni delle applicazioni.

Sebbene non sia scritto specificamente per tvOS, questo documento fornisce informazioni su come configurare e usare questo portale per preparare l'app per la pubblicazione nell'App Store di Apple TV. Molti degli stessi passaggi sono necessari se si sta configurando un'app iOS o tvOS.

Dopo aver completato tutti i passaggi elencati in precedenza, vedere la pagina relativa alla [configurazione dell'app tvOS in iTunes Connect](~/ios/tvos/deploy-test/app-distribution/itunes-connect.md) per aggiungere le informazioni specifiche dell'app tvOS che saranno necessarie.

È importante tenere presente che solo gli sviluppatori che appartengono al **programma Apple Developer** hanno accesso a iTunes Connect. I membri del **programma Apple Developer Enterprise** non hanno accesso.

Se si verificano problemi durante l'invio dell'app Novell. tvOS all'App Store di Apple TV, vedere la guida alla [risoluzione dei problemi](~/ios/tvos/troubleshooting.md) . Contiene diversi problemi noti che possono verificarsi e come risolverli in Novell. tvOS.

Per altre informazioni, visitare la guida alla pubblicazione nella pagina relativa all' [App Store di Apple TV](~/ios/tvos/deploy-test/app-distribution/app-store-publishing.md) .

<a name="In-House-Distribution"></a>

## <a name="in-house-distribution"></a>Distribuzione interna

La distribuzione interna, chiamata a volte *distribuzione aziendale*, consente a tutti i membri del **programma Apple Developer Enterprise** di distribuire le app internamente ad altri membri della stessa organizzazione. La distribuzione interna presenta il vantaggio di non dover esaminare l'App Store e di poter installare un'applicazione in un numero illimitato di dispositivi. È tuttavia importante tenere presente che i membri del **programma Apple Developer Enterprise****non** hanno accesso a iTunes Connect e quindi il licenziatario è responsabile della distribuzione dell'app.

Per ulteriori informazioni sulla configurazione e su come distribuire l'applicazione internamente, consultare la [Guida alla distribuzione interna](~/ios/deploy-test/app-distribution/in-house-distribution.md). Questo documento è specifico per iOS, ma le stesse tecniche vengono usate per le app tvOS.

<a name="Ad_Hoc_Distribution"></a>

## <a name="ad-hoc-distribution"></a>Distribuzione ad hoc

Le app Novell. tvOS possono essere testate dagli utenti tramite la distribuzione ad hoc, disponibile sia nel **programma Apple Developer**che nel **programma Apple Developer Enterprise**, che consente di testare fino a 100 dispositivi Apple TV. Il caso d'uso migliore per la distribuzione ad hoc è la distribuzione all'interno dell'azienda quando iTunes Connect non è un'opzione.

Per ulteriori informazioni sulla configurazione e su come distribuire l'app internamente, vedere la [Guida alla distribuzione ad hoc](~/ios/deploy-test/app-distribution/ad-hoc-distribution.md). Anche in questo caso, questo documento è specifico per iOS, ma le stesse tecniche vengono usate per le app tvOS.

<a name="Summary"></a>

## <a name="summary"></a>Summary

Questo articolo ha fornito una breve panoramica dei meccanismi di distribuzione disponibili per le app Novell. tvOS. Ha introdotto l'App Store di Apple TV, ad hoc e la distribuzione interna e fornito collegamenti a informazioni più dettagliate.

## <a name="related-links"></a>Collegamenti correlati

- [Esempi di tvOS](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+tvOS)
- [tvOS](https://developer.apple.com/tvos/)
- [Guide all'interfaccia umana tvOS](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Guida alla programmazione delle app per tvOS](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
