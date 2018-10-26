---
title: Configurare l'App tvOS in iTunes Connect
description: Questo articolo fornisce una Guida aggiuntiva per configurare l'App in iTunes Connect per configurazioni specifiche tvOS iOS.
ms.prod: xamarin
ms.assetid: 86C7C5BD-C97D-4F1D-B611-A7694557BFDF
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/16/2017
ms.openlocfilehash: 3f4ef00cfe990de2d5afd461d7a110d32bc4a236
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2018
ms.locfileid: "50108817"
---
# <a name="configure-your-tvos-app-in-itunes-connect"></a>Configurare l'App tvOS in iTunes Connect

_Questo articolo fornisce una Guida aggiuntiva per configurare l'App in iTunes Connect per configurazioni specifiche tvOS iOS._


Oltre all'impostazione che sarà necessario apportare seguendo iOS e le configurazioni [configurare l'App in iTunes Connect](~/ios/deploy-test/app-distribution/app-store-distribution/itunesconnect.md) , questo documento vengono trattate le configurazioni specifiche che saranno necessario per rilasciare un xamarin. tvos app di Store di App Apple TV.

<a name="Adding-a-tvOS-Release-Version" />

## <a name="adding-a-tvos-release-version"></a>Aggiunta di una versione finale di tvOS

Se si sta creando una nuova App per essere rilasciata il Store di App Apple TV o aggiunta del supporto Apple TV a un'app iOS esistente, è necessario avere creato un iTunes Connect Record e averlo configurato specifico usando iOS seguenti guide:

- [Creazione di un record iTunes Connect](~/ios/deploy-test/app-distribution/app-store-distribution/itunesconnect.md#creating)
- [Gestione di video e screenshot dell'app](~/ios/deploy-test/app-distribution/app-store-distribution/itunesconnect.md#managing)
- [Gestione di nome, descrizione, novità, parole chiave e URL](~/ios/deploy-test/app-distribution/app-store-distribution/itunesconnect.md#metadata)
- [Gestione delle informazioni generali](~/ios/deploy-test/app-distribution/app-store-distribution/itunesconnect.md#general)

Facoltativamente, potrebbe richiedere anche:

- [Gestione delle informazioni in Game Center](~/ios/deploy-test/app-distribution/app-store-distribution/itunesconnect.md#game-center)
- [Gestione delle informazioni sull'acquisto in-app](~/ios/deploy-test/app-distribution/app-store-distribution/itunesconnect.md#iap)

Con tutti i passaggi precedenti completato, aprire Record iTunes Connect e scegliere di aggiungere supporto per tvOS mediante l'intestazione laterale a sinistra dell'app:

[![](itunes-connect-images/connect01.png "Aggiungere supporto per tvOS mediante l'intestazione laterale a sinistra")](itunes-connect-images/connect01.png#lightbox)

Le schermate di informazioni specifiche di tvOS saranno quindi disponibili per il determinato Record iTunes Connect:

[![](itunes-connect-images/connect02.png "La schermata di informazioni specifiche di tvOS")](itunes-connect-images/connect02.png#lightbox)

<a name="tvOS-Version-Information" />

## <a name="tvos-version-information"></a>informazioni sulla versione di tvOS

Dalla barra laterale a sinistra, selezionare **1.0 Prepare per l'invio** sotto la sezione APP tvOS:

[![](itunes-connect-images/connect03.png "informazioni sulla versione di tvOS")](itunes-connect-images/connect03.png#lightbox)

In questa schermata specificare le informazioni seguenti:

- Le necessarie schermate, descrizione, parole chiave e URL.
- Informazioni generali sull'App, ad esempio numero di versione, Copyright e classificazione di età.
- Acquisti In-App facoltativi.
- Supporto facoltativo Game Center con tabelloni punteggi e i risultati ottenuti.
- Informazioni di revisione dell'App, ad esempio contatti, account dimostrativi e note di richieste.

Dopo aver immesso le informazioni necessarie, scegliere il **salvare** pulsante nell'angolo superiore destro dello schermo per salvare le modifiche:

[![](itunes-connect-images/connect04.png "tvOS pronte per l'invio di informazioni sulla versione")](itunes-connect-images/connect04.png#lightbox)

<a name="Submitting-for-Review" />

## <a name="preparing-to-submit-for-review"></a>Preparazione per l'invio per la revisione

Quando si è finalmente pronta per inviare l'app xamarin. tvos alla Store di App TV Apple per la revisione, tornare alla finestra Record iTunes dell'app Connect e fare clic sui **Invia per la revisione** pulsante nell'angolo superiore destro dello schermo:

[![](itunes-connect-images/connect05.png "Invia per la revisione")](itunes-connect-images/connect05.png#lightbox)

<a name="Summary" />

## <a name="summary"></a>Riepilogo

Questo articolo ha offerto panoramica dell'impostazione specifici tvOS necessaria in iTunes Connect per rilasciare un'app tvOS la Store di App Apple TV.



## <a name="related-links"></a>Collegamenti correlati

- [Esempi di tvOS](https://developer.xamarin.com/samples/tvos/all/)
- [tvOS](https://developer.apple.com/tvos/)
- [le guide dell'interfaccia umana tvOS](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Guida alla programmazione di App per tvOS](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
