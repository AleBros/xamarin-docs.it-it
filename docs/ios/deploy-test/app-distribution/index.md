---
title: Panoramica della distribuzione di app Novell. iOS
description: Questo documento offre una panoramica delle tecniche di distribuzione disponibili per le applicazioni Xamarin.iOS e contiene i collegamenti a documenti più dettagliati sull'argomento.
ms.prod: xamarin
ms.assetid: 341D36DB-BB07-FA94-BCC9-5F8C0B18C179
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/18/2017
ms.openlocfilehash: 20126849027f735e9ecd3599c290b4e7a57f837e
ms.sourcegitcommit: ec62e2624295aa502ec35ac782031d61d61c3aaa
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/11/2020
ms.locfileid: "75886580"
---
# <a name="xamarinios-app-distribution-overview"></a>Panoramica della distribuzione di app Novell. iOS

_Questo documento offre una panoramica delle tecniche di distribuzione disponibili per le applicazioni Xamarin.iOS e contiene i collegamenti a documenti più dettagliati sull'argomento._

Dopo che un'app Xamarin.iOS è stata sviluppata, il passaggio successivo del ciclo di vita dello sviluppo software è la distribuzione dell'app agli utenti, come illustrato nella sezione evidenziata del diagramma seguente:

[![dopo che l'app iOS è stata sviluppata, il passaggio successivo consiste nel distribuire l'app agli utenti, come illustrato nella sezione evidenziata di questo diagramma](images/publishingdiagram.png)](images/publishingdiagram.png#lightbox)

Apple fornisce i modi seguenti per distribuire un'applicazione iOS:

- [**App Store**](#app-store-distribution)
- [**Interna (Enterprise)** ](#in-house-distribution)
- [**Ad hoc**](#ad-hoc-distribution)
- [**App personalizzate per le aziende**](#custom-apps-for-business)

In tutti questi scenari è necessario effettuare il provisioning delle applicazioni usando il *profilo di provisioning* appropriato. I profili di provisioning sono file che contengono le informazioni sulla firma del codice, oltre all'identità dell'applicazione e al meccanismo di distribuzione previsto. Per la distribuzione senza App Store contengono anche informazioni sui dispositivi in cui l'app può essere distribuita.

## <a name="app-store-distribution"></a>Distribuzione di App Store

> [!IMPORTANT]
> Apple [ha indicato](https://developer.apple.com/ios/submit/) che a partire da marzo 2019 tutte le app e gli aggiornamenti inviati all'App Store devono essere stati compilati con iOS 12.1 SDK o versioni successive, disponibili in Xcode 10.1 o versioni successive.
> Le app devono anche supportare iPhone Xs e le dimensioni dello schermo 12,9" di iPad Pro.

Si tratta della principale modalità di distribuzione delle applicazioni iOS ai clienti sui dispositivi iOS. Tutte le app inviate all'App Store richiedono l'approvazione di Apple.

Le app vengono inviate all'App Store tramite un portale chiamato *iTunes Connect*. La guida [Configure your App in iTunes Connect](~/ios/deploy-test/app-distribution/app-store-distribution/itunesconnect.md) (Configurare l'app in iTunes Connect) contiene altre informazioni su come configurare e usare questo portale per preparare un'app Xamarin.iOS per la pubblicazione nell'App Store.

È importante tenere presente che solo gli sviluppatori che appartengono al **programma Apple Developer** hanno accesso a iTunes Connect. I membri del **programma Apple Developer Enterprise** non hanno accesso.

Per altre informazioni, vedere la guida [Distribuzione tramite l'App Store](~/ios/deploy-test/app-distribution/app-store-distribution/index.md).

## <a name="in-house-distribution"></a>Distribuzione interna

La distribuzione interna, chiamata a volte *distribuzione aziendale*, consente a tutti i membri del **programma Apple Developer Enterprise** di distribuire le app internamente ad altri membri della stessa organizzazione. La distribuzione interna presenta il vantaggio di non dover esaminare l'App Store e di poter installare un'applicazione in un numero illimitato di dispositivi. È tuttavia importante tenere presente che i membri del **programma Apple Developer Enterprise** **non** hanno accesso a iTunes Connect e quindi il licenziatario è responsabile della distribuzione dell'app.

Per ulteriori informazioni sulla configurazione e su come distribuire un'applicazione internamente, consultare la [Guida alla distribuzione interna](~/ios/deploy-test/app-distribution/in-house-distribution.md).

## <a name="ad-hoc-distribution"></a>Distribuzione ad hoc

Le applicazioni Xamarin.iOS possono essere testate dagli utenti tramite la distribuzione ad hoc, disponibile sia nel **programma Apple Developer** che nel **programma Apple Developer Enterprise**, che consente di testare fino a 100 dispositivi iOS. Il caso d'uso ideale per la distribuzione ad hoc è la distribuzione in una società quando iTunes Connect non è disponibile.

Per ulteriori informazioni sulla configurazione e su come distribuire un'applicazione internamente, consultare la [Guida alla distribuzione ad hoc](~/ios/deploy-test/app-distribution/ad-hoc-distribution.md).

## <a name="custom-apps-for-business"></a>App personalizzate per le aziende

Apple consente la [distribuzione personalizzata](https://developer.apple.com/business/custom-apps/) delle app alle aziende e alla formazione. Per informazioni, vedere la [Guida dell'utente di Apple Business Manager](https://support.apple.com/guide/apple-business-manager/welcome/web) .

## <a name="related-links"></a>Collegamenti correlati

- [Distribuzione di App Store](~/ios/deploy-test/app-distribution/app-store-distribution/index.md)
- [Configurazione di un'app in iTunes Connect](~/ios/deploy-test/app-distribution/app-store-distribution/itunesconnect.md)
- [Pubblicazione nell'App Store](~/ios/deploy-test/app-distribution/app-store-distribution/publishing-to-the-app-store.md)
- [Distribuzione interna](~/ios/deploy-test/app-distribution/in-house-distribution.md)
- [Distribuzione ad hoc](~/ios/deploy-test/app-distribution/ad-hoc-distribution.md)
- [File iTunesMetadata.plist](~/ios/deploy-test/app-distribution/itunesmetadata.md)
- [Supporto IPA](~/ios/deploy-test/app-distribution/ipa-support.md)
- [Risoluzione dei problemi](~/ios/deploy-test/troubleshooting.md)
