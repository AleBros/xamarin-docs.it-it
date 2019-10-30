---
title: Profili di provisioning per le app Xamarin.Mac
description: Questa guida descrive come creare i profili di provisioning necessari per pubblicare un'app Xamarin.Mac.
ms.prod: xamarin
ms.assetid: bdff6c32-f7e3-4a97-a093-dbda48be8227
ms.technology: xamarin-mac
author: davidortinau
ms.author: daortin
ms.date: 04/12/2017
ms.openlocfilehash: b361cd00121d16752d5eebc971653c8ea185c641
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73018100"
---
# <a name="provisioning-profiles-for-xamarinmac-apps"></a>Profili di provisioning per le app Xamarin.Mac

I profili di provisioning consentono a uno sviluppatore di incorporare nelle app Xamarin.Mac diverse funzionalità specifiche di macOS (precedentemente noto come Mac OS X), ad esempio iCloud e le notifiche push. Lo sviluppatore deve creare, scaricare e installare un profilo di provisioning Mac per ogni applicazione in fase di sviluppo che userà queste funzionalità.

[![](profiles-images/certif13.png "The Apple Provisioning Portal")](profiles-images/certif13.png#lightbox)

<a name="Development_Provisioning_Profile" />

## <a name="development-provisioning-profile"></a>Profilo di provisioning di sviluppo

Un profilo di provisioning di sviluppo consente a un'app pensata per Mac App Store di essere sottoposta a test nei computer specifici che sono stati configurati nel profilo. Questo aspetto è particolarmente importante quando si usano funzionalità macOS come iCloud e le notifiche push.

> [!NOTE]
> Lo sviluppatore deve avere già creato un certificato di sviluppo Mac prima di potere creare un profilo di provisioning di sviluppo. Specificare le informazioni richieste come illustrato in questa schermata per generare un **profilo di provisioning di sviluppo** che può essere usato per creare le build. Devono essere disponibili un certificato di sviluppo Mac valido per la selezione nella casella **Certificate** (Certificato) e almeno un sistema registrato per il test.

Procedere come descritto di seguito:

1. Selezionare il tipo di profilo di provisioning che occorre creare e fare clic sul pulsante **Continue** (Continua): 

    [![](profiles-images/certif14.png "Selecting the profile type")](profiles-images/certif14.png#lightbox)
2. Selezionare l'ID dell'applicazione per cui creare il profilo e fare clic sul pulsante **Continue** (Continua): 

    [![](profiles-images/certif15.png "Selecting the app ID")](profiles-images/certif15.png#lightbox)
3. Selezionare l'ID sviluppatore usato per firmare il profilo e fare clic su **Continue** (Continua): 

    [![](profiles-images/certif16.png "Selecting the developer ID")](profiles-images/certif16.png#lightbox)
4. Selezionare i computer in cui può essere usato il profilo e fare clic su **Continue** (Continua): 

    [![](profiles-images/certif17.png "Selecting the allowed computers")](profiles-images/certif17.png#lightbox)
5. A questo punto, immettere un nome in **Profile Name** (Nome profilo) e fare clic sul pulsante **Generate** (Genera): 

    [![](profiles-images/certif18.png "Generating the profile")](profiles-images/certif18.png#lightbox)
6. Fare clic sul pulsante **Download** per scaricare il nuovo profilo: 

    [![](profiles-images/certif19.png "Downloading the profile")](profiles-images/certif19.png#lightbox)
7. I profili di provisioning di sviluppo sono installati nel riquadro Profiles Preferences (Preferenze profilo) dell'applicazione Mac **Preferenze di sistema**: 

    [![](profiles-images/certif20.png "Installing the profile")](profiles-images/certif20.png#lightbox)
8. Nel riquadro Profiles Preferences (Preferenze profilo) saranno visualizzati tutti i profili installati: 

    [![](profiles-images/image47.png "Showing all installed profiles")](profiles-images/image47.png#lightbox)
9. Il profilo verrà visualizzato anche nell'**utilità del certificato dello sviluppatore** nel caso in cui debba essere scaricato nuovamente: 

    [![](profiles-images/image48.png "The Developer Certificate Utility")](profiles-images/image48.png#lightbox)

Sarà necessario creare un nuovo profilo di provisioning di sviluppo per ogni nuova app o quando viene aggiunto un nuovo computer in cui eseguire il test.

<a name="Production_Provisioning_Profile" />

## <a name="production-provisioning-profile"></a>Profilo di provisioning di produzione

I profili di provisioning di produzione sono necessari per creare un pacchetto da inviare a Mac App Store.

Procedere come descritto di seguito:

1. Selezionare il tipo di profilo da creare e fare clic sul pulsante **Continue** (Continua): 

    [![](profiles-images/certif21.png "Selecting the type of profile")](profiles-images/certif21.png#lightbox)
2. Selezionare l'ID dell'app per cui creare il profilo e fare clic sul pulsante **Continue** (Continua): 

    [![](profiles-images/certif15.png "Selecting the app ID")](profiles-images/certif15.png#lightbox)
3. Selezionare l'ID aziendale per la firma del profilo e fare clic sul pulsante **Continue** (Continua): 

    [![](profiles-images/certif23.png "Selecting the company ID")](profiles-images/certif23.png#lightbox)
4. Immettere un nome in **Profile name** (Nome profilo) e fare clic sul pulsante **Generate** (Genera): 

    [![](profiles-images/certif24.png "Generating the profile")](profiles-images/certif24.png#lightbox)
5. Fare clic su **Download** per ottenere il file del profilo di provisioning (con estensione `.provisionprofile`): 

    [![](profiles-images/certif25.png "Downloading the profile")](profiles-images/certif25.png#lightbox)
6. Trascinarlo in **Xcode Organizer** o fare doppio clic per installarlo. Il profilo verrà quindi visualizzato in Xcode Organizer: 

    [![](profiles-images/image51.png "Installing the profile")](profiles-images/image51.png#lightbox)
7. Il profilo di provisioning verrà inoltre visualizzato nell'elenco: 

    [![](profiles-images/certif26.png "Showing the installed profiles")](profiles-images/certif26.png#lightbox)

Se lo sviluppatore decide di cambiare le funzionalità usate da un ID app (ad esempio abilitando iCloud o le notifiche push), dovrà ricreare i profili di provisioning per tale ID app.

## <a name="related-links"></a>Collegamenti correlati

- [Installazione](~//mac/get-started/installation.md)
- [Esempio Hello, Mac](~//mac/get-started/hello-mac.md)
- [Distribuire le app in Mac App Store](https://developer.apple.com/devcenter/mac/checklist/)
- [Guida degli strumenti: firma del codice dell'app](https://developer.apple.com/library/mac/#documentation/ToolsLanguages/Conceptual/OSXWorkflowGuide/CodeSigning/CodeSigning.html)
- [ID sviluppatore e GateKeeper](https://developer.apple.com/resources/developer-id/)
