---
title: Uso delle funzionalità in Xamarin.iOS
description: L'aggiunta di funzionalità a un'applicazione spesso richiede un'ulteriore configurazione del provisioning. Questa guida illustra la configurazione necessaria per tutte le funzionalità.
ms.prod: xamarin
ms.assetid: 98A4676F-992B-4593-8D38-6EEB2EB0801C
ms.technology: xamarin-ios
author: asb3993
ms.author: amburns
ms.date: 05/06/2018
ms.openlocfilehash: 7bb4e142a8b7bd0cf0691da381729dc226028193
ms.sourcegitcommit: 1dd7d09b60fcb1bf15ba54831ed3dd46aa5240cb
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70121364"
---
# <a name="working-with-capabilities-in-xamarinios"></a>Uso delle funzionalità in Xamarin.iOS

_L'aggiunta di funzionalità a un'applicazione spesso richiede un'ulteriore configurazione del provisioning. Questa guida illustra la configurazione necessaria per tutte le funzionalità._

Apple offre agli sviluppatori alcune _capacità_, spesso chiamate _servizi app_, che consentono di estendere le funzionalità e di ampliare le potenzialità dell'uso delle app iOS. Le funzionalità consentono agli sviluppatori di aggiungere all'applicazione una maggiore integrazione delle funzioni della piattaforma, ad esempio: la possibilità di avviare transazioni monetarie dall'app, servizi del dispositivo aggiuntivi, come Siri, e altro ancora.
Queste funzionalità possono essere usate con i progetti Xamarin.iOS. L'elenco completo dei servizi è il seguente:

- Gruppi di app
- Domini associati
- Protezione dati
- Game Center
- HealthKit
- HomeKit
- Configurazione accessori wireless
- iCloud
- Acquisto in-app
- Audio Inter-App
- Apple Pay
- Wallet
- Notifica push
- VPN personale
- Siri
- Mappe
- Modalità in background
- Condivisione del keychain
- Estensioni di rete
- Configurazione area sensibile
- A percorsi multipli
- Lettura di tag NFC

Le funzionalità possono essere abilitate tramite Visual Studio per Mac e Visual Studio 2019 oppure manualmente in Apple Developer Portal. Per alcune funzionalità, ad esempio, Wallet, Apple Pay e iCloud, è necessaria una configurazione aggiuntiva degli ID delle app.

Questa guida illustra come abilitare ognuno di questi servizi app nell'applicazione sia automaticamente in Visual Studio sia manualmente tramite il centro sviluppatori, inclusa l'eventuale configurazione aggiuntiva necessaria. 

## <a name="adding-app-services"></a>Aggiunta di servizi app

Per usare le funzionalità, l'app deve avere un profilo di provisioning valido contenente un ID app con il servizio corretto abilitato. La creazione di questo profilo di provisioning può essere eseguita in Visual Studio per Mac e Visual Studio 2019 oppure manualmente nel centro Apple Developer.

Questa sezione illustra come usare il provisioning automatico di Visual Studio o il centro sviluppatori per abilitare la maggior parte delle funzionalità. Alcune funzionalità, ad esempio Wallet, iCloud, Apple Pay e i gruppi di app, che richiedono una configurazione aggiuntiva, sono illustrate in dettaglio nelle guide specifiche.

> [!IMPORTANT]
> Non tutte le funzionalità possono essere aggiunte e gestite in Visual Studio con il provisioning automatico. L'elenco seguente contiene le funzionalità supportate:
>
>- HealthKit 
>- HomeKit 
>- VPN personale 
>- Configurazione accessori wireless 
>- Audio Inter-App 
>- SiriKit 
>- Hotspot 
>- Estensioni di rete 
>- Lettura di tag NFC
>- A percorsi multipli 
>
>Le funzionalità Notifiche push, Game Center, Acquisto in-app, Mappe, Condivisione del keychain, Domini associati e Protezione dati non sono attualmente supportate. Per aggiungere queste funzionalità, usare il provisioning manuale e seguire i passaggi della sezione [Centro sviluppatori](#devcenter).

## <a name="using-the-ide"></a>Utilizzo di IDE

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

Le funzionalità vengono aggiunte a **Entitlements.plist** in Visual Studio per Mac. Per aggiungere funzionalità, seguire questa procedura:

1. Aprire il file **Info.plist** dell'applicazione iOS e selezionare lo schema **Automatically Provisioning** (Provisioning automatico) e il **Team** nella casella combinata. Seguire i passaggi della guida [Provisioning automatico](~/ios/get-started/installation/device-provisioning/automatic-provisioning.md), se necessario:

    ![Opzione Automatically manage signing (Gestisci automaticamente la firma)](images/manage-signing.png)

2. Aprire il file **Entitlements.plist** e selezionare la funzionalità che si vuole aggiungere:

    ![Aggiungere le funzionalità al file entitlements.plist](images/image17.png)

    La selezione di una funzionalità consente di ottenere due risultati:
    - Aggiunta di tale funzionalità all'ID app
    - Aggiunta della coppia chiave/valore degli entitlement al file Entitlements.plist.

    Visual Studio per Mac avviserà quando queste attività saranno state completate visualizzando il messaggio di operazione riuscita seguente:

    ![Aggiungere le funzionalità al file entitlements.plist](images/image18.png)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Le funzionalità vengono aggiunte a **Entitlements.plist**. Per aggiungere funzionalità in Visual Studio 2019, seguire questa procedura:

1. Associare Visual Studio 2019 a un computer Mac, come descritto nella guida [Associazione al Mac](~/ios/get-started/installation/windows/connecting-to-mac/index.md).

2. Aprire le opzioni di provisioning, selezionando **Progetto > Provisioning Properties (Proprietà di provisioning)**

3. Selezionare lo schema **Automatically Provisioning** (Provisioning automatico) e il **Team** nella casella combinata. Seguire i passaggi della guida [Provisioning automatico](~/ios/get-started/installation/device-provisioning/automatic-provisioning.md), se necessario:

    ![Opzione Automatically manage signing (Gestisci automaticamente la firma)](images/manage-signing-vs.png)

4. Aprire il file **Entitlements.plist** e selezionare la funzionalità che si vuole aggiungere. Salvare il file.

    Quando si salva **Entitlement.plist** vengono eseguite due operazioni:

    - Aggiunta di tale funzionalità all'ID app
    - Aggiunta della coppia chiave/valore degli entitlement al file Entitlements.plist.

-----


<a name="devcenter" />

## <a name="using-the-developer-center"></a>Uso del centro sviluppatori

L'uso del centro sviluppatori è un processo in due passaggi che richiede la creazione di un ID app e quindi l'uso di tale ID app per creare un profilo di provisioning. Questi passaggi sono indicati in dettaglio di seguito.

### <a name="creating-an-app-id-with-an-app-service"></a>Creazione di un ID app con un servizio app

1. Passare al [centro sviluppatori Apple](https://developer.apple.com/account) su un Mac (il computer host di compilazione se si usa un computer Windows) ed eseguire l'accesso.
2. Selezionare **Certificates, Identifiers, and Profiles** (Certificati, identificatori e profili):

    ![Centro sviluppatori Apple](images/image5.png)

3. In **Identifiers** (Identificatori) selezionare **App IDs** (ID app):

    ![Sezione degli ID app nel centro sviluppatori](images/image6.png)

4. Fare clic sul pulsante **+** nell'angolo in alto a destra per creare un nuovo ID app.
5. Immettere una descrizione dell'ID app e immettere un ID bundle nel formato `com.domain.appname`. Questo ID bundle deve corrispondere all'ID bundle nel progetto:

    ![Aggiunta dei dettagli dell'ID app](images/image7.png)

6. In **App Services** (Servizi app) selezionare il servizio o i servizi necessari nell'app:

    ![Pagina di selezione dei servizi app](images/image8.png)

7. Fare clic su **Continue** (Continua).
8. Confermare l'ID app. Ogni servizio può essere in uno dei seguenti stati: **Enabled** (Abilitato), **Disabled** (Disabilitato) o **Configurable** (Configurabile), come illustrato di seguito. Se è **Enabled** (Abilitato), è possibile usarlo in un profilo di provisioning. Se è **Configurable** (Configurabile), è necessaria un'ulteriore configurazione per questa funzionalità. Questi passaggi aggiuntivi sono descritti più dettagliatamente nelle sezioni successive.

    ![Conferma dell'ID app](images/image9.png)

9. Fare clic su **Register** (Registra) e quindi su **Done** (Fine). Il nuovo ID app creato verrà visualizzato nell'elenco di ID app iOS.


<a name="provisioningprofile" />

### <a name="creating-a-provisioning-profile"></a>Creazione di un profilo di provisioning

Creare ora un profilo di provisioning contenente tale ID app. Attenersi ai passaggi indicati di seguito:

1. Nel centro sviluppatori Apple passare a **Provisioning Profiles > All** (Profili di provisioning > Tutti):

    ![Sezione dei profili di provisioning](images/image10.png)

2. Fare clic sul pulsante **+** nell'angolo in alto a destra per creare un nuovo profilo di provisioning.
3. Selezionare il tipo di profilo di provisioning necessario e fare clic su **Continue** (Continua):

    ![Selezione del profilo di provisioning](images/image11.png)

4. Dall'elenco a discesa selezionare l'ID app creato nei passaggi precedenti e fare clic su **Continue** (Continua):

    ![Selezione di un ID app](images/image12.png)

5. Selezionare i certificati usati per firmare l'app e fare clic su **Continue** (Continua):

    ![Selezione dei certificati](images/image13.png)

6. Selezionare i dispositivi da includere in questo profilo e fare clic su **Continue** (Continua):

    ![Selezionare i dispositivi per il profilo di provisioning](images/image14.png)

7. Assegnare un nome al profilo per poterlo identificare e fare clic su **Continue** (Continua) per generare il profilo:

    ![Assegnare un nome al profilo di provisioning](images/image15.png)

8. Fare clic sul pulsante **Download** (Scarica) per scaricarlo e fare doppio clic sul file in Finder per installare il profilo di provisioning.

9. Se si usa Visual Studio, verificare che sia selezionata l'opzione **Provisioning manuale**.

10. In Visual Studio per Mac/Visual Studio passare a **Opzioni progetto > Firma del bundle** e impostare il profilo di provisioning su quello appena creato:

    ![Opzioni progetto di Visual Studio per Mac](images/image16.png)

> [!IMPORTANT]
> Potrebbe anche essere necessario impostare le chiavi per gli entitlement nel file Entitlements.plist e le chiavi per la privacy nel file Info.plist. Per altre informazioni su questi entitlement, vedere la guida [Uso degli entitlement](~/ios/deploy-test/provisioning/entitlements.md).

<a name="nextsteps" />

## <a name="next-steps"></a>Passaggi successivi

Dopo che una funzionalità è stata abilitata sul lato server, sono necessarie ancora alcune operazioni per consentire all'app di usarla. Nell'elenco seguente vengono descritti i passaggi aggiuntivi che potrebbero essere necessari:

- Usare lo spazio dei nomi del framework nell'app.
- Aggiungere all'app gli entitlement necessari. Per informazioni dettagliate sugli entitlement necessari e su come aggiungerli, vedere la guida [Uso degli entitlement](~/ios/deploy-test/provisioning/entitlements.md).

<a name="troubleshooting" />

## <a name="troubleshooting-capabilities"></a>Risoluzione dei problemi relativi alle funzionalità

Il seguente è un elenco dettagliato di alcuni dei problemi più comuni che possono costituire un ostacolo quando si sviluppa un'app con un servizio app abilitato.

- Assicurarsi che l'ID corretto sia stato creato e registrato accuratamente nella sezione **Certificates, IDs & Profiles** (Certificati, ID e profili) del portale per sviluppatori di Apple.
- Assicurarsi che il servizio sia stato aggiunto all'ID dell'app (o dell'estensione) e che il servizio sia configurato per usare il gruppo di app/ID esercente/contenitore creato sopra in **Certificates, IDs & Profiles** (Certificati, ID e profili) del portale per sviluppatori di Apple.
- Assicurarsi che i profili di provisioning e gli ID delle app siano stati installati e che il file **Info.plist** dell'app (nel progetto Xamarin) usi uno degli ID app configurati sopra.
- Assicurarsi che il file **Entitlements.plist** dell'app (nel progetto Xamarin) abbia il servizio corretto abilitato.
- Assicurarsi che in info.plist siano impostate le chiavi per la privacy appropriate
- In **Firma del bundle iOS** dell'app assicurarsi che **Entitlement personalizzati** sia impostato su **Entitlements.plist**. Questa _non_ è l'impostazione predefinita per le build di debug e del simulatore iOS.

<a name="summary" />

## <a name="summary"></a>Summary

Questa guida ha illustrato le funzionalità, ovvero i _servizi app_, e ha descritto come possono essere abilitate in Visual Studio e nel centro sviluppatori Apple. Ha anche spiegato in dettaglio come configurare servizi più complessi, ad esempio Wallet, iCloud, Apple Pay e Gruppi di app. Ha infine illustrato i passaggi successivi per ottenere le opzioni per la configurazione e la risoluzione di problemi semplici.
