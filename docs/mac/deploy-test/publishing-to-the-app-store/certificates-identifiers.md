---
title: Certificati e identificatori in Xamarin.Mac
description: Questa guida descrive come creare i certificati e gli identificatori necessari per pubblicare un'app Xamarin.Mac.
ms.prod: xamarin
ms.assetid: 393d0066-7f6f-4ac3-a48d-4b5db65bc4cd
ms.technology: xamarin-mac
author: davidortinau
ms.author: daortin
ms.date: 12/17/2019
ms.openlocfilehash: 2b2bfe9925a99c2ba7f1366ea28d5c72e2e1da88
ms.sourcegitcommit: db422e33438f1b5c55852e6942c3d1d75dc025c4
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/24/2020
ms.locfileid: "76725541"
---
# <a name="certificates-and-identifiers-in-xamarinmac"></a>Certificati e identificatori in Xamarin.Mac

_Questa guida descrive come creare i certificati e gli identificatori necessari per pubblicare un'app Xamarin.Mac._

## <a name="setup"></a>Configurazione

Visitare [Apple Developer Member Center](https://developer.apple.com) per configurare il Mac per lo sviluppo. Fare clic sul collegamento **Account** e accedere. Il menu principale è riportato di seguito:

> [!div class="mx-imgBorder"]
> [![Apple Developer Member Center](certificates-identifiers-images/devcenter01.png)](certificates-identifiers-images/devcenter01-large.png#lightbox)

Fare clic sul pulsante **Certificates, Identifiers & Profiles** (Certificati, identificatori e profili) o sul pulsante più accanto all'intestazione **Certificates** (Certificati):

> [!div class="mx-imgBorder"]
> [![Selezione di Certificates, IDs & Profiles (Certificati, ID e profili)](certificates-identifiers-images/devcenter02.png)](certificates-identifiers-images/devcenter02-large.png#lightbox)

Selezionare un tipo di certificato e fare clic su **Continue** (Continua):

> [!div class="mx-imgBorder"]
> [![Selezione del collegamento Certificates (Certificati)](certificates-identifiers-images/devcenter03.png)](certificates-identifiers-images/devcenter03-large.png#lightbox)

Da qui è possibile scaricare i **certificati intermedi** (Worldwide Developer Relations Certificate Authority e Developer ID Certificate Authority), se necessario (ultima voce in fondo alla pagina). Tali certificati dovrebbero comunque essere automaticamente configurati per lo sviluppatore da Xcode.

Il resto di questa sezione illustra le sezioni rilevanti per gli sviluppatori Mac:

- **Registrare l'ID app Mac**: lo sviluppatore dovrà seguire la procedura seguente per ogni applicazione scritta.
- **Registrare sistemi macOS**: questo passaggio è richiesto solo per aggiungere computer con cui eseguire i test.
- **Creare certificati**: questo passaggio è richiesto solo una prima volta per la configurazione dei certificati e successivamente per il rinnovo.
- **Creare un profilo di provisioning**: lo sviluppatore dovrà seguire questa procedura per ogni nuova applicazione scritta e in caso di aggiunta di nuovi sistemi.

## <a name="register-mac-app-id"></a>Registrare l'ID app Mac

È necessario registrare un ID app per ogni applicazione. Seguire questa procedura per creare una voce:

1. Premere il segno più (+) o fare clic su **Register an App ID** (Registra ID app):

    > [!div class="mx-imgBorder"]
    > [![Introduzione agli ID app](certificates-identifiers-images/appid01.png)](certificates-identifiers-images/appid01-large.png#lightbox)

1. Scegliere **App IDs** (ID app)

    > [!div class="mx-imgBorder"]
    > [![Introduzione agli ID app](certificates-identifiers-images/appid02.png)](certificates-identifiers-images/appid02-large.png#lightbox)

1. Immettere una descrizione in **Description** (Descrizione) e selezionare gli eventuali servizi richiesti dall'applicazione in **App Services** (Servizi app): La piattaforma deve essere **macOS**. Scegliere una descrizione in **Description** (usata solo in questo portale). Immettere un ID in **Bundle ID**, corrispondente a **Info.plist**. Selezionare le funzionalità richieste dall'app

    > [!div class="mx-imgBorder"]
    > [![Immissione della descrizione e selezione dei servizi app](certificates-identifiers-images/appid03.png)](certificates-identifiers-images/appid03-large.png#lightbox)

    Scegliere **Continue** (Continue) per verificare le selezioni.

1. Se le informazioni sono corrette, fare clic su **Register** (Registra) per completare la configurazione:

    > [!div class="mx-imgBorder"]
    > [![Verificare i dati immessi](certificates-identifiers-images/appid04.png)](certificates-identifiers-images/appid04-large.png#lightbox)

1. Verificare le informazioni e fare clic sul pulsante **Submit** (Invia):

    > [!div class="mx-imgBorder"]
    > ![Verifica delle informazioni](certificates-identifiers-images/appid05.png)

Alcuni **servizi app** potrebbero richiedere un'ulteriore configurazione (ad esempio, iCloud). In questo caso, selezionare il nuovo ID app appena creato e fare clic sul pulsante **Edit** (Modifica):

> [!div class="mx-imgBorder"]
> [![Modifica del nuovo ID app](certificates-identifiers-images/appid06.png)](certificates-identifiers-images/appid06-large.png#lightbox)

Per configurare i servizi iCloud, ad esempio, fare clic sul pulsante **Edit** (Modifica):

> [!div class="mx-imgBorder"]
> [![Configurazione dei servizi iCloud](certificates-identifiers-images/appid07.png)](certificates-identifiers-images/appid07-large.png#lightbox)

## <a name="register-macos-devices"></a>Registrare i dispositivi macOS

Per creare un profilo di provisioning per il test, lo sviluppatore dovrà disporre di computer Mac registrati. Per i test è possibile registrare un massimo di 100 computer.

1. In Mac Developer Center selezionare **All** (Tutti) nella sezione **Devices** (Dispositivi) e fare clic sul pulsante **+** :

    > [!div class="mx-imgBorder"]
    > [![Aggiunta di un nuovo computer](certificates-identifiers-images/device01.png)](certificates-identifiers-images/device01-large.png#lightbox)

1. Immettere un **nome** e l'**UUID** del computer da aggiungere e quindi fare clic sul pulsante **Continue** (Continua). Rivedere le informazioni e fare clic sul pulsante **Register** (Registra):

    > [!div class="mx-imgBorder"]
    > [![Immissione delle informazioni sul nuovo computer](certificates-identifiers-images/device02.png)](certificates-identifiers-images/device02-large.png#lightbox)

1. Verificare e confermare i dati immessi:

    > [!div class="mx-imgBorder"]
    > [![Immissione delle informazioni sul nuovo computer](certificates-identifiers-images/device03.png)](certificates-identifiers-images/device03-large.png#lightbox)

## <a name="create-certificates"></a>Creare certificati

Usare la sezione Certificates (Certificati) per creare diversi tipi di certificati che saranno usati per firmare le applicazioni Mac:

> [!div class="mx-imgBorder"]
> [![Creazione di un nuovo certificato](certificates-identifiers-images/devcenter04.png)](certificates-identifiers-images/devcenter04-large.png#lightbox)

Sono disponibili cinque tipi principali di certificato relativi allo sviluppo macOS:

- **Sviluppo Mac** - Facoltativo per lo sviluppo di app generali, ma obbligatorio se lo sviluppatore prevede di usare funzionalità quali iCloud o notifiche push. Lo sviluppatore avrà bisogno di un certificato di sviluppo prima di poter creare profili di provisioning per l'accesso a tali funzionalità.
- **Distribuzione di app Mac** - Lo sviluppatore dovrà disporre di un certificato per l'app e di un altro certificato per il programma di installazione.
- **Distribuzione del programma di installazione Mac** - Lo sviluppatore dovrà disporre di un certificato per l'app e di un altro certificato per il programma di installazione.
- **Programma di installazione ID sviluppatore** - Certificati per il programma di installazione da distribuire all'esterno di Mac App Store.
- **Applicazione ID sviluppatore** - Certificati per l'app da distribuire all'esterno di Mac App Store.

Le sezioni seguenti forniscono esempi della creazione di alcuni di questi tipi di certificati.

### <a name="mac-development-certificate"></a>Certificato di sviluppo Mac

Come accennato in precedenza, il certificato di sviluppo Mac è richiesto solo se vengono usate funzionalità di macOS come iCloud o le notifiche push.

Eseguire le operazioni seguenti per creare un nuovo certificato di sviluppo:

1. Selezionare il pulsante di opzione **Mac Development** (Sviluppo Mac) e fare clic su **Continue** (Continua):

    > [!div class="mx-imgBorder"]
    > [![Aggiunta di un certificato di sviluppo](certificates-identifiers-images/certif02.png)](certificates-identifiers-images/certif02-large.png#lightbox)

1. Caricare una _richiesta di firma del certificato_. Il file di richiesta del certificato (con estensione `.certSigningRequest`) verrà salvato localmente nel Mac. Fare clic su **Choose File** (Scegli file) per selezionare la richiesta di certificato e quindi scegliere **Continue** (Continua).

    > [!div class="mx-imgBorder"]
    > [![Caricare un file di richiesta di certificato](certificates-identifiers-images/certif03.png)](certificates-identifiers-images/certif03-large.png#lightbox)

    Per istruzioni su come usare **Keychain Access** per creare un file di richiesta di certificato, seguire il collegamento [Altre informazioni >](https://help.apple.com/developer-account/#/devbfa00fef7).

1. Fare clic su **Download** per ottenere il file del certificato e fare doppio clic per installarlo:

    > [!div class="mx-imgBorder"]
    > [![Scaricare il file del certificato](certificates-identifiers-images/certif04.png)](certificates-identifiers-images/certif04-large.png#lightbox)

Come accennato in precedenza, il certificato dello sviluppatore non è sempre richiesto, a meno che lo sviluppatore non stia implementando funzionalità macOS come iCloud e le notifiche push. È inoltre necessario per creare un **profilo di provisioning di sviluppo**, che sarà richiesto per testare le app di Mac App Store.

### <a name="mac-app-store-certificates"></a>Certificati Mac App Store

Per rilasciare un'app in App Store, saranno necessari due certificati:

- Il certificato di **distribuzione dell'app Mac** che verrà usato per firmare l'applicazione; e
- Il certificato per la **distribuzione del programma di installazione Mac**, per firmare il programma di installazione.

> [!TIP]
> Prestare attenzione quando si assegna un nome alle richieste di certificati per queste chiavi: usare nomi descrittivi che includano il testo `Application` e `Installer`, in modo che possano essere distinti in un secondo momento.

Per prima cosa, creare il certificato del programma di installazione:

1. Selezionare **Mac Installer Distribution** (Distribuzione programma di installazione Mac) come tipo di certificato e fare clic sul pulsante **Continue** (Continua):

    > [!div class="mx-imgBorder"]
    > [![Creazione di un certificato App Store](certificates-identifiers-images/certif05.png)](certificates-identifiers-images/certif05-large.png#lightbox)

1. La pagina successiva illustra come usare l'**accesso keychain** per generare un file di richiesta del certificato. Seguire le istruzioni:

    > [!div class="mx-imgBorder"]
    > [![Caricare una richiesta di certificato](certificates-identifiers-images/certif06.png)](certificates-identifiers-images/certif06-large.png#lightbox)

    Per istruzioni su come usare **Keychain Access** per creare un file di richiesta di certificato, seguire il collegamento [Altre informazioni >](https://help.apple.com/developer-account/#/devbfa00fef7). Ricordarsi di scegliere un nome di certificato che rifletta il _tipo_ di certificato (applicazione o programma di installazione).

1. Fare clic su **Download** per ottenere il certificato e fare doppio clic per installarlo nel **keychain**:

    > [!div class="mx-imgBorder"]
    > [![Download del certificato di App Store](certificates-identifiers-images/certif07.png)](certificates-identifiers-images/certif07-large.png#lightbox)

**Seguire la stessa procedura per il certificato di distribuzione dell'app Mac.**

![Certificato di distribuzione di app Mac](certificates-identifiers-images/certif08.png)

### <a name="developer-id-certificates"></a>Certificati ID sviluppatore

Per rilasciare in autonomia un'applicazione Xamarin.Mac (non rilasciata tramite l'App Store di Apple), saranno necessari due certificati:

- Il certificato **programma di installazione ID sviluppatore** che verrà usato per firmare l'applicazione, e
- Il certificato **applicazione ID sviluppatore** per firmare il programma di installazione.

> [!TIP]
> Prestare attenzione quando si assegna un nome alle richieste di certificati per queste chiavi: usare nomi descrittivi che includano il testo `Application` e `Installer`, in modo che possano essere distinti in un secondo momento.

Dopo aver creato, scaricato e installato i certificati, saranno visibili in **Keychain Access**:

[Elenco di certificati in Keychain Access](certificates-identifiers-images/certif09.png)

## <a name="related-links"></a>Collegamenti correlati

- [Installazione](/visualstudio/mac/installation/)
- [Esempio Hello, Mac](~/mac/get-started/hello-mac.md)
- [Distribuire le app in Mac App Store](https://developer.apple.com/devcenter/mac/checklist/)
- [ID sviluppatore e GateKeeper](https://developer.apple.com/developer-id/)
