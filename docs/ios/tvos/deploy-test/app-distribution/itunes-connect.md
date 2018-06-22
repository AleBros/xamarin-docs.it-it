---
title: Configurare l'App tvOS in iTunes Connect
description: Questo articolo fornisce una Guida supplementare per configurare l'App in iTunes Connect per le configurazioni specifiche tvOS iOS.
ms.prod: xamarin
ms.assetid: 86C7C5BD-C97D-4F1D-B611-A7694557BFDF
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/16/2017
ms.openlocfilehash: d082a980572349da1b7e6155b3aa4de41512796f
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2018
ms.locfileid: "30779826"
---
# <a name="configure-your-tvos-app-in-itunes-connect"></a>Configurare l'App tvOS in iTunes Connect

_Questo articolo fornisce una Guida supplementare per configurare l'App in iTunes Connect per le configurazioni specifiche tvOS iOS._


Oltre alle configurazioni e impostazioni che è necessario creare seguendo iOS [configurata l'applicazione in iTunes Connect](~/ios/deploy-test/app-distribution/app-store-distribution/itunesconnect.md) , questo documento vengono trattate le configurazioni specifiche che saranno necessario rilasciare un Xamarin.tvOS App in App Store di Apple TV.

<a name="Adding-a-tvOS-Release-Version" />

## <a name="adding-a-tvos-release-version"></a>Aggiunta di una versione tvOS

Se si sta creando una nuova App per il rilascio App Store di Apple TV, o aggiunge supporto per Apple TV a un'app iOS esistente, è necessario avere creato un iTunes Connect Record e configurato utilizzando il seguente iOS guide specifiche:

- [Creazione di un record iTunes Connect](~/ios/deploy-test/app-distribution/app-store-distribution/itunesconnect.md#creating)
- [Gestione di video e screenshot dell'app](~/ios/deploy-test/app-distribution/app-store-distribution/itunesconnect.md#managing)
- [Gestione di nome, descrizione, novità, parole chiave e URL](~/ios/deploy-test/app-distribution/app-store-distribution/itunesconnect.md#metadata)
- [Gestione delle informazioni generali](~/ios/deploy-test/app-distribution/app-store-distribution/itunesconnect.md#general)

Facoltativamente, è anche necessario:

- [Gestione delle informazioni in Game Center](~/ios/deploy-test/app-distribution/app-store-distribution/itunesconnect.md#game-center)
- [Gestione delle informazioni sull'acquisto in-app](~/ios/deploy-test/app-distribution/app-store-distribution/itunesconnect.md#iap)

Con tutti i passaggi precedenti completati, aprire l'app in iTunes Connect Record e selezionare questa opzione per aggiungere il supporto di tvOS utilizzando il riquadro a sinistra:

[![](itunes-connect-images/connect01.png "Aggiungere il supporto di tvOS utilizzando il riquadro a sinistra")](itunes-connect-images/connect01.png#lightbox)

Le schermate di informazioni specifiche tvOS quindi saranno disponibili per iTunes Connect Record specificato:

[![](itunes-connect-images/connect02.png "La schermata di informazioni specifiche tvOS")](itunes-connect-images/connect02.png#lightbox)

<a name="tvOS-Version-Information" />

## <a name="tvos-version-information"></a>informazioni sulla versione tvOS

Dal menu laterale a sinistra, selezionare **1.0 Preparazione per l'invio di** nella sezione tvOS APP:

[![](itunes-connect-images/connect03.png "informazioni sulla versione tvOS")](itunes-connect-images/connect03.png#lightbox)

In questa schermata fornire le informazioni seguenti:

- Necessarie schermate, descrizione, le parole chiave e gli URL.
- Informazioni generali App, ad esempio il numero di versione e sul Copyright di età.
- Acquisti In-App facoltativi.
- Supporto facoltativo Game Center con classifiche e i risultati ottenuti.
- Le informazioni di revisione di App, ad esempio contatto, account Demo e note necessarie.

Dopo aver immesso le informazioni necessarie, fare clic su di **salvare** pulsante nell'angolo superiore destro dello schermo per salvare le modifiche:

[![](itunes-connect-images/connect04.png "tvOS pronto per l'invio di informazioni sulla versione")](itunes-connect-images/connect04.png#lightbox)

<a name="Submitting-for-Review" />

## <a name="preparing-to-submit-for-review"></a>Preparazione per l'invio per la revisione

Quando si è pronto per inviare l'app Xamarin.tvOS in App Store di Apple TV per la revisione, tornare a iTunes dell'app Record di connessione e fare clic il **inviare per la revisione** pulsante nell'angolo superiore destro dello schermo:

[![](itunes-connect-images/connect05.png "Inviare per la revisione")](itunes-connect-images/connect05.png#lightbox)

<a name="Summary" />

## <a name="summary"></a>Riepilogo

In questo articolo è stato assegnato panoramica dell'impostazione specifici tvOS necessario in iTunes Connect per il rilascio di un'app tvOS in App Store di Apple TV.



## <a name="related-links"></a>Collegamenti correlati

- [Esempi di tvOS](https://developer.xamarin.com/samples/tvos/all/)
- [tvOS](https://developer.apple.com/tvos/)
- [tvOS Guide interfaccia umana](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Guida alla programmazione di App per tvOS](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
