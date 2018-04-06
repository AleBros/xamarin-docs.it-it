---
title: Panoramica della distribuzione di app
description: Questo documento offre una panoramica delle tecniche di distribuzione disponibili per le applicazioni Xamarin.iOS e contiene i collegamenti a documenti più dettagliati sull'argomento.
ms.prod: xamarin
ms.assetid: 341D36DB-BB07-FA94-BCC9-5F8C0B18C179
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/18/2017
ms.openlocfilehash: 76de5b46559676e36b9d1f9b71575323beec414e
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2018
---
# <a name="app-distribution-overview"></a>Panoramica della distribuzione di app

_Questo documento offre una panoramica delle tecniche di distribuzione disponibili per le applicazioni Xamarin.iOS e contiene i collegamenti a documenti più dettagliati sull'argomento._

Dopo che un'app Xamarin.iOS è stata sviluppata, il passaggio successivo del ciclo di vita dello sviluppo software è la distribuzione dell'app agli utenti, come illustrato nella sezione evidenziata del diagramma seguente:


[![](images/publishingdiagram.png "Dopo che l'app iOS è stata sviluppata, il passaggio successivo è la distribuzione dell'app agli utenti, come illustrato nella sezione evidenziata di questo diagramma")](images/publishingdiagram.png#lightbox)


Apple consente di distribuire un'applicazione iOS nei modi seguenti, supportati da Xamarin.iOS:

1. [**App Store**](#App_Store_Distribution)
2. [**In-House (Enterprise)**](#In-House_Distribution)
2. [**Ad hoc**](#Ad_Hoc_Distribution)

In tutti questi scenari è necessario effettuare il provisioning delle applicazioni usando il *profilo di provisioning* appropriato. I profili di provisioning sono file che contengono le informazioni sulla firma del codice, oltre all'identità dell'applicazione e al meccanismo di distribuzione previsto. Per la distribuzione senza App Store contengono anche informazioni sui dispositivi in cui l'app può essere distribuita.

<a name="App_Store_Distribution"/>

## <a name="app-store-distribution"></a>Distribuzione tramite l'App Store

Si tratta della principale modalità di distribuzione delle applicazioni iOS ai clienti sui dispositivi iOS. Tutte le app inviate all'App Store richiedono l'approvazione di Apple.

Le app vengono inviate all'App Store tramite un portale chiamato *iTunes Connect*. La guida [Configure your App in iTunes Connect](~/ios/deploy-test/app-distribution/app-store-distribution/itunesconnect.md) (Configurare l'app in iTunes Connect) contiene altre informazioni su come configurare e usare questo portale per preparare un'app Xamarin.iOS per la pubblicazione nell'App Store.

È importante tenere presente che solo gli sviluppatori che appartengono al **programma Apple Developer** hanno accesso a iTunes Connect. I membri del **programma Apple Developer Enterprise** non hanno accesso.

Per altre informazioni, vedere la guida [Distribuzione tramite l'App Store](~/ios/deploy-test/app-distribution/app-store-distribution/index.md).

<a name="In-House_Distribution"/>

## <a name="in-house-distribution"></a>Distribuzione interna

La distribuzione interna, chiamata a volte *distribuzione aziendale*, consente a tutti i membri del **programma Apple Developer Enterprise** di distribuire le app internamente ad altri membri della stessa organizzazione. La distribuzione interna presenta il vantaggio di non dover esaminare l'App Store e di poter installare un'applicazione in un numero illimitato di dispositivi. È tuttavia importante tenere presente che i membri del **programma Apple Developer Enterprise** **non** hanno accesso a iTunes Connect e quindi il licenziatario è responsabile della distribuzione dell'app.

Per altre informazioni sulla configurazione e su come distribuire un'applicazione internamente, vedere la [guida Distribuzione interna](~/ios/deploy-test/app-distribution/in-house-distribution.md).

<a name="Ad_Hoc_Distribution"/>

## <a name="ad-hoc-distribution"></a>Distribuzione ad hoc

Le applicazioni Xamarin.iOS possono essere testate dagli utenti tramite la distribuzione ad hoc, disponibile sia nel **programma Apple Developer** che nel **programma Apple Developer Enterprise**, che consente di testare fino a 100 dispositivi iOS. Il caso d'uso ideale per la distribuzione ad hoc è la distribuzione in una società quando iTunes Connect non è disponibile.

Per altre informazioni sulla configurazione e su come distribuire un'applicazione internamente, vedere la [guida Distribuzione ad hoc](~/ios/deploy-test/app-distribution/ad-hoc-distribution.md).

## <a name="summary"></a>Riepilogo

Questo articolo ha offerto una breve panoramica dei meccanismi di distribuzione disponibili per le applicazioni Xamarin.iOS. Ha presentato la distribuzione tramite l'iTunes App Store, ad hoc e interna e ha fornito collegamenti a informazioni più dettagliate.

## <a name="related-links"></a>Collegamenti correlati

- [Distribuzione tramite l'App Store](~/ios/deploy-test/app-distribution/app-store-distribution/index.md)
- [Configurazione di un'app in iTunes Connect](~/ios/deploy-test/app-distribution/app-store-distribution/itunesconnect.md)
- [Pubblicazione nell'App Store](~/ios/deploy-test/app-distribution/app-store-distribution/publishing-to-the-app-store.md)
- [Distribuzione interna](~/ios/deploy-test/app-distribution/in-house-distribution.md)
- [Distribuzione ad hoc](~/ios/deploy-test/app-distribution/ad-hoc-distribution.md)
- [File iTunesMetadata.plist](~/ios/deploy-test/app-distribution/itunesmetadata.md)
- [Supporto IPA](~/ios/deploy-test/app-distribution/ipa-support.md)
- [Risoluzione dei problemi](~/ios/deploy-test/troubleshooting.md)
