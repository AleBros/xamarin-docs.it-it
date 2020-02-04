---
title: Pubblicazione di app Xamarin.Mac in Mac App Store
description: Questo documento descrive come distribuire un'app Xamarin.Mac con Visual Studio per Mac. Illustra come configurare un account Mac Developer, come creare i certificati per la firma del codice e come usarli per creare app Mac distribuibili direttamente o tramite Mac App Store.
ms.prod: xamarin
ms.assetid: D26C5E54-EAD2-5487-264D-4263AEA1EBF2
ms.technology: xamarin-mac
author: davidortinau
ms.author: daortin
ms.date: 03/14/2017
ms.openlocfilehash: 1a14bcc15299360a17b4c9e7a363b6d6e6f2e69a
ms.sourcegitcommit: db422e33438f1b5c55852e6942c3d1d75dc025c4
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/24/2020
ms.locfileid: "76725025"
---
# <a name="publishing-xamarinmac-apps-to-the-mac-app-store"></a>Pubblicazione di app Xamarin.Mac in Mac App Store

## <a name="overview"></a>Panoramica

Le app Xamarin.Mac possono essere distribuite in due modi:

- **ID sviluppatore**: le app firmate con un ID sviluppatore possono essere distribuite fuori dall'App Store ma sono riconosciute da GateKeeper e autorizzate per l'installazione.
- **Mac App Store**: per essere inviate a Mac App Store, le app devono avere un pacchetto di installazione e sia l'app sia il programma di installazione devono essere firmati.

Questo documento illustra come usare Visual Studio per Mac e Xcode per configurare un account Apple Developer e come configurare un progetto Xamarin.Mac per ogni tipo di distribuzione.

## <a name="mac-developer-program"></a>Programma Mac Developer

Quando si aderisce al [programma Mac Developer](https://developer.apple.com/devcenter/mac/) è possibile scegliere se aderire come Individual (Singolo) o Company (Società), come illustrato nella schermata seguente:

[![Portale Apple Developer](images/image1.png "Portale Apple Developer")](images/image1-large.png#lightbox)

Scegliere il tipo di registrazione in base alle esigenze.

> [!NOTE]
> Dalle scelte effettuate in questo contesto dipendono le schermate e le opzioni visualizzate quando si configura un account per sviluppatore. Le descrizioni e le schermate di questo documento sono quelle visualizzate quando si sceglie un account sviluppatore **Individual** (Singolo). Nel caso dell'account **Company** (Società), alcune opzioni sono disponibili solo per gli **amministratori del team**.

### <a name="certificates-and-identifiersmacdeploy-testpublishing-to-the-app-storecertificates-identifiersmd"></a>[Certificati e identificatori](~/mac/deploy-test/publishing-to-the-app-store/certificates-identifiers.md)

Questa guida descrive come creare i certificati e gli identificatori necessari per pubblicare un'app Xamarin.Mac.

### <a name="create-provisioning-profilemacdeploy-testpublishing-to-the-app-storeprofilesmd"></a>[Creare un profilo di provisioning](~/mac/deploy-test/publishing-to-the-app-store/profiles.md)

Questa guida descrive come creare i profili di provisioning necessari per pubblicare un'app Xamarin.Mac.

### <a name="mac-app-configurationmacdeploy-testpublishing-to-the-app-storeapp-configurationmd"></a>[Configurazione di app Mac](~/mac/deploy-test/publishing-to-the-app-store/app-configuration.md)

Questa guida descrive come configurare un'app Xamarin.Mac per la pubblicazione.

### <a name="sign-with-developer-idmacdeploy-testpublishing-to-the-app-storesigningmd"></a>[Sign with Developer ID](~/mac/deploy-test/publishing-to-the-app-store/signing.md) (Accedere con un ID sviluppatore)

Questa guida descrive come firmare un'app Xamarin.Mac con un ID sviluppatore per la pubblicazione.

### <a name="bundle-for-mac-app-storemacdeploy-testpublishing-to-the-app-storebundlingmd"></a>[Bundle per Mac App Store](~/mac/deploy-test/publishing-to-the-app-store/bundling.md)

Questa guida descrive come creare il bundle di un'app Xamarin.Mac per la pubblicazione in Mac App Store.

### <a name="upload-to-mac-app-storemacdeploy-testpublishing-to-the-app-storeuploadingmd"></a>[Caricamento in Mac App Store](~/mac/deploy-test/publishing-to-the-app-store/uploading.md)

Questa guida descrive come caricare un'app Xamarin.Mac per la pubblicazione in Mac App Store.

## <a name="related-links"></a>Collegamenti correlati

- [Installazione](/visualstudio/mac/installation/)
- [Esempio Hello, Mac](~/mac/get-started/hello-mac.md)
- [ID sviluppatore e GateKeeper](https://developer.apple.com/developer-id/)
