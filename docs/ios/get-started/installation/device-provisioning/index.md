---
title: Provisioning di dispositivi per Xamarin.iOS
description: Questo documento descrive come eseguire il provisioning di un dispositivo in modo da usarlo per il test di un'applicazione. Illustra inoltre come configurare un'app per poter usare funzionalità quali le notifiche push.
ms.prod: xamarin
ms.assetid: CACA5236-3C90-F6DF-FD4E-0797B61670CE
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 05/06/2018
ms.openlocfilehash: bb1ef1e948c796e6b0d91230be3c82c5a8c7d366
ms.sourcegitcommit: eedc6032eb5328115cb0d99ca9c8de48be40b6fa
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/07/2020
ms.locfileid: "78910850"
---
# <a name="device-provisioning-for-xamarinios"></a>Provisioning di dispositivi per Xamarin.iOS

Durante lo sviluppo di un'applicazione Xamarin.iOS è molto importante testare l'app distribuendola in un dispositivo fisico oltre che nel simulatore. I bug specifici del dispositivo e i problemi di prestazioni possono emergere solo quando l'app viene eseguita in un dispositivo, a causa dei limiti dell'hardware, ad esempio la memoria o la connettività di rete. Per eseguire il test in un dispositivo fisico, il dispositivo deve essere *sottoposto a provisioning* ed è necessario informare Apple che tale dispositivo sarà usato a scopo di test.

Le sezioni evidenziate nell'immagine seguente illustrano i passaggi necessari per configurare il provisioning iOS:

[![](images/provisioningdiagram.png "The highlighted sections in this image show the steps required to get set up for iOS provisioning")](images/provisioningdiagram.png#lightbox)

Il passaggio successivo prevede la distribuzione dell'applicazione. Per altre informazioni sulla distribuzione, vedere la guida [App Distribution](~/ios/deploy-test/app-distribution/index.md) (Distribuzione dell'app)

Prima di distribuire l'applicazione in un dispositivo, è necessario avere una sottoscrizione attiva al programma per sviluppatori di Apple, *o* usare il [provisioning gratuito](~/ios/get-started/installation/device-provisioning/free-provisioning.md). Apple offre due tipi di programma:

- **Apple Developer Program**: Apple Developer Program consente sia a singoli sviluppatori sia a organizzazioni di sviluppare, testare e distribuire le app.
- **Apple Developer Enterprise Program**: Apple Developer Enterprise Program è un programma più adatto alle organizzazioni che vogliono sviluppare e distribuire le app solo internamente. I membri del programma Enterprise non hanno accesso a iTunes Connect e le app create non possono essere pubblicate nell'App Store.

Per registrarsi a uno di questi programmi, visitare il [portale Apple Developer](https://developer.apple.com/programs/enroll/). Si noti che per registrarsi come sviluppatore Apple, è necessario essere in possesso di un [ID Apple](https://appleid.apple.com/). In questa guida si presuppone di **essere** un membro di Apple Developer Program.

In alternativa, Apple ha introdotto il [provisioning gratuito](~/ios/get-started/installation/device-provisioning/free-provisioning.md) in Xcode 7 che consente di eseguire una singola applicazione in un unico dispositivo *senza* essere membro di Apple Developer Program. Se si effettua il provisioning in questo modo, esistono alcune limitazioni, come descritto [qui](~/ios/get-started/installation/device-provisioning/free-provisioning.md#limitations) nel dettaglio.

Tutte le applicazioni eseguite in un dispositivo devono includere un set di metadati (o un'*identificazione personale*) contenente le informazioni sull'applicazione e sullo sviluppatore. Apple usa questa identificazione personale per assicurarsi che l'applicazione non venga manomessa quando viene distribuita o eseguita in un dispositivo utente. A questo scopo, agli sviluppatori di app viene chiesto di registrare l'ID Apple come sviluppatore, configurare un ID app, richiedere un certificato e registrare il dispositivo nel quale viene distribuita l'applicazione.

Quando un'applicazione viene distribuita in un dispositivo, nel dispositivo iOS viene installato anche un profilo di provisioning. Il profilo di provisioning serve a verificare le che l'app è stata firmata in fase di compilazione ed è firmata crittograficamente da Apple. Insieme, il profilo di provisionig e i controlli di identificazione personale determinano se un'applicazione può essere distribuita in un dispositivo eseguendo le verifiche seguenti:

- **Chi** (certificati: l'app è stata firmata con una chiave privata, che ha a una chiave pubblica corrispondente nel profilo di provisioning? Il certificato associa anche lo sviluppatore a un team di sviluppo)
- **Cosa** (ID app individuale: l'identificatore del bundle impostato nel file Info.plist corrisponde all'ID app nel profilo di provisioning?)
- **Dove** (dispositivi: il dispositivo è contenuto nel profilo di provisioning?)

Questi passaggi servono a garantire che tutto ciò che viene creato o usato durante il processo di sviluppo, tra cui le applicazioni e i dispositivi, possano essere riconducibili a un account sviluppatore Apple.

## <a name="provisioning-your-device"></a>Provisioning del dispositivo

Esistono due modi per effettuare il provisioning del dispositivo iOS:

- **Automaticamente (scelta consigliata)** : selezionare l'opzione **Automatically manage signing** (Gestire la firma automaticamente) nel file Info.plist in modo che Visual Studio per Mac crei e gestisca automaticamente le identità di firma, gli ID app e i profili di provisioning. Per informazioni su come gestire automaticamente il provisioning, vedere la guida [Automatic Provisioning](automatic-provisioning.md) (Provisioning automatico). Questo è il modo consigliato per effettuare il provisioning di un dispositivo iOS.

- **Manualmente**: è possibile creare e gestire le identità di firma, gli ID app e i profili di provisioning tramite il portale Apple Developer, come descritto nella guida [Provisioning manuale](manual-provisioning.md). Questi elementi possono quindi essere gestiti come descritto nella guida [Apple Account Management](~/cross-platform/macios/apple-account-management.md) (Gestione degli account Apple).

## <a name="provisioning-for-application-services"></a>Povisioning per i servizi per le applicazioni

Apple offre una selezione di servizi speciali per le applicazioni, denominati anche funzionalità, che possono essere attivati per un'applicazione Xamarin.iOS. Questi servizi per le applicazioni devono essere configurati sia nel portale di provisioning iOS quando viene creato l'**ID app** sia nel file **Entitlements.plist** che è parte del progetto dell'applicazione Xamarin.iOS. Per informazioni sull'aggiunta dei servizi per le applicazioni all'app, vedere la guida [Introduction to Capabilities](~/ios/deploy-test/provisioning/capabilities/index.md) (Introduzione alle funzionalità) e la guida [Working with Entitlements](~/ios/deploy-test/provisioning/entitlements.md) (Uso degli entitlement).

- Creare un ID app con i servizi app necessari.
- Creare un nuovo [profilo di provisioning](#provisioning-your-device) contenente tale ID app.
- Impostare gli entitlement nel progetto Xamarin.iOS

## <a name="related-links"></a>Collegamenti correlati

- [Provisioning gratuito](~/ios/get-started/installation/device-provisioning/free-provisioning.md)
- [Distribuzione di app](~/ios/deploy-test/app-distribution/index.md)
- [Risoluzione dei problemi](~/ios/deploy-test/troubleshooting.md)
- [Apple - App Distribution Guide](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/AppDistributionGuide/Introduction/Introduction.html) (Apple - Guida alla distribuzione dell'app)
