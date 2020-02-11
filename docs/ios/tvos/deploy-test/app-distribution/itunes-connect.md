---
title: Configurare l'app tvOS in iTunes Connect
description: Questo articolo fornisce una Guida aggiuntiva a iOS configurare l'app in iTunes Connect per le configurazioni specifiche di tvOS.
ms.prod: xamarin
ms.assetid: 86C7C5BD-C97D-4F1D-B611-A7694557BFDF
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/16/2017
ms.openlocfilehash: 01ab48f68656dcabdf2a6cfc286dfcd8850454f8
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73030755"
---
# <a name="configure-your-tvos-app-in-itunes-connect"></a>Configurare l'app tvOS in iTunes Connect

_Questo articolo fornisce una Guida aggiuntiva a iOS configurare l'app in iTunes Connect per le configurazioni specifiche di tvOS._

Oltre alle configurazioni e alle impostazioni che è necessario eseguire seguendo la Guida [configurare l'app in iTunes Connect](~/ios/deploy-test/app-distribution/app-store-distribution/itunesconnect.md) per iOS, questo documento illustra le configurazioni specifiche che saranno necessarie per rilasciare un'app Xamarin.TvOS nell'app Apple TV Negozio.

<a name="Adding-a-tvOS-Release-Version" />

## <a name="adding-a-tvos-release-version"></a>Aggiunta di una versione di rilascio di tvOS

Se si crea una nuova app da rilasciare nell'App Store di Apple TV o si aggiunge il supporto Apple TV a un'app iOS esistente, è necessario avere creato un record iTunes Connect e configurarlo usando le seguenti guide specifiche per iOS:

- [Creazione di un record iTunes Connect](~/ios/deploy-test/app-distribution/app-store-distribution/itunesconnect.md#creating)
- [Gestione di video e screenshot dell'app](~/ios/deploy-test/app-distribution/app-store-distribution/itunesconnect.md#managing)
- [Gestione di nome, descrizione, novità, parole chiave e URL](~/ios/deploy-test/app-distribution/app-store-distribution/itunesconnect.md#metadata)
- [Gestione delle informazioni generali](~/ios/deploy-test/app-distribution/app-store-distribution/itunesconnect.md#general)

Facoltativamente, potrebbe essere necessario anche:

- [Gestione delle informazioni in Game Center](~/ios/deploy-test/app-distribution/app-store-distribution/itunesconnect.md#game-center)
- [Gestione delle informazioni sull'acquisto in-app](~/ios/deploy-test/app-distribution/app-store-distribution/itunesconnect.md#iap)

Con tutti i passaggi precedenti completati, aprire il record iTunes Connect dell'app e selezionare per aggiungere il supporto tvOS usando la barra laterale a sinistra:

[![](itunes-connect-images/connect01.png "Add tvOS support using the left hand sidebar")](itunes-connect-images/connect01.png#lightbox)

Le schermate di informazioni specifiche di tvOS saranno quindi disponibili per il record iTunes Connect specificato:

[![](itunes-connect-images/connect02.png "The tvOS specific information screen")](itunes-connect-images/connect02.png#lightbox)

<a name="tvOS-Version-Information" />

## <a name="tvos-version-information"></a>Informazioni sulla versione di tvOS

Dalla barra laterale a sinistra selezionare **1,0 preparare l'invio** nella sezione app tvOS:

[![](itunes-connect-images/connect03.png "tvOS Version Information")](itunes-connect-images/connect03.png#lightbox)

In questa schermata sono disponibili le seguenti informazioni:

- Schermate, descrizione, parole chiave e URL richiesti.
- Informazioni generali sull'app, ad esempio il numero di versione, il copyright e la classificazione dell'età.
- Acquisti in-app facoltativi.
- Supporto Game Center facoltativo con classifiche e obiettivi.
- Informazioni di revisione dell'app necessarie, ad esempio contatto, account demo e note.

Dopo aver immesso le informazioni necessarie, fare clic sul pulsante **Salva** nell'angolo superiore destro della schermata per salvare le modifiche:

[![](itunes-connect-images/connect04.png "tvOS Version Information ready for submission")](itunes-connect-images/connect04.png#lightbox)

<a name="Submitting-for-Review" />

## <a name="preparing-to-submit-for-review"></a>Preparazione dell'invio per la revisione

Quando si è pronti per inviare l'app Xamarin.tvOS all'App Store di Apple TV per la revisione, tornare al record iTunes Connect dell'app e fare clic sul pulsante **Invia per la revisione** nell'angolo superiore destro della schermata:

[![](itunes-connect-images/connect05.png "Submit for Review")](itunes-connect-images/connect05.png#lightbox)

<a name="Summary" />

## <a name="summary"></a>Riepilogo

Questo articolo ha fornito una panoramica dell'impostazione specifica tvOS necessaria in iTunes Connect per rilasciare un'app tvOS all'App Store di Apple TV.

## <a name="related-links"></a>Collegamenti correlati

- [Esempi di tvOS](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+tvOS)
- [tvOS](https://developer.apple.com/tvos/)
- [Guide all'interfaccia umana tvOS](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Guida alla programmazione delle app per tvOS](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
