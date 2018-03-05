---
title: Distribuzione ad hoc
description: Questo documento offre una panoramica delle tecniche di distribuzione ad hoc usate principalmente per testare le applicazioni Xamarin.iOS con un gruppo numeroso di persone.
ms.topic: article
ms.prod: xamarin
ms.assetid: 3B621CAD-103C-478A-97C3-829015F48D1A
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/19/2017
ms.openlocfilehash: 423240949daf45d8d179a3ca9f89677f490cc24d
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2018
---
# <a name="ad-hoc-distribution"></a>Distribuzione ad hoc

_Questo documento offre una panoramica delle tecniche di distribuzione ad hoc usate principalmente per testare le applicazioni Xamarin.iOS con un gruppo numeroso di persone._

Dopo che un'app Xamarin.iOS è stata sviluppata, il passaggio successivo del ciclo di vita dello sviluppo software è la distribuzione dell'app agli utenti per il test.

iTunes Connect è una delle opzioni per gestire il test dell'app, che viene descritta più dettagliatamente nella guida a [TestFlight](~/ios/deploy-test/testflight.md). I membri del programma Apple Developer Enterprise non hanno tuttavia accesso a iTunes Connect, quindi la distribuzione *ad hoc* è il metodo migliore per testare queste app.

Le applicazioni Xamarin.iOS possono essere testate dagli utenti tramite la distribuzione *ad hoc*, disponibile sia nel programma Apple Developer che nel programma Apple Developer Enterprise, che consente di testare fino a 100 dispositivi iOS.

La distribuzione ad hoc ha il vantaggio di non richiedere l'approvazione dell'App Store e di poter essere installata in modalità wireless da un server Web o tramite iTunes. È tuttavia limitata a **100** dispositivi per ogni anno di appartenenza, sia per lo sviluppo che per la distribuzione, e i dispositivi devono essere aggiunti manualmente nel Member Center usando il valore UDID. Per altre informazioni sull'aggiunta di dispositivi, vedere la guida [Device Provisioning](~/ios/get-started/installation/device-provisioning/manual-provisioning.md#adddevice) (Provisioning di dispositivi).

Per la distribuzione ad hoc, è necessario effettuare il provisioning delle applicazioni usando un *profilo di provisioning* ad hoc contenente le informazioni sulla firma del codice, oltre all'identità dell'applicazione e ai dispositivi che possono installare l'applicazione.

Questa guida fornisce informazioni sul provisioning per la distribuzione ad hoc e su come distribuire un'app Xamarin.iOS.

<a name="setup" />

## <a name="setting-up-for-distribution"></a>Configurazione per la distribuzione

Anche se si prevede di rilasciare un'applicazione Xamarin.iOS per la distribuzione interna, a scopo di testing, sarà necessario compilare un profilo di provisioning per la distribuzione ad hoc specifico. Questo profilo consente di applicare una firma digitale a un'applicazione per il rilascio in modo che possa essere installata in un dispositivo iOS.

La sezione successiva illustra come eseguire la configurazione con un certificato di distribuzione e un profilo di provisioning di distribuzione.

> [!NOTE]
>  Nota: solo gli agenti e gli amministratori del team possono creare i certificati di distribuzione e i profili di provisioning.

<a name="createcertificate" />

## <a name="create-a-distribution-certificate"></a>Creare un certificato di distribuzione


1. Passare alla sezione *Certificates, Identifiers & Profiles* (Certificati, identificatori e profili) di Apple Developer Member Center.
2. In *Certificates* (Certificati) selezionare **Production** (Produzione).
3. Fare clic sul pulsante **+** per creare un nuovo certificato.
4. Nell'intestazione *Production* (Produzione) selezionare **In-House and Ad Hoc** (Interna e ad hoc) o **App Store and Ad Hoc** (App Store e ad hoc), in base all'appartenenza al programma:

  [ ![](ad-hoc-distribution-images/cert-first-small.png "Selezionare In-House and Ad Hoc (Interna e ad hoc) o App Store and Ad Hoc (App Store e ad hoc)")](ad-hoc-distribution-images/cert-first-large.png)

5. Fare clic su Continue (Continua) e seguire le istruzioni per creare una richiesta di firma del certificato tramite l'accesso keychain:

  [ ![](ad-hoc-distribution-images/createcertmanually02.png "Creare una richiesta di firma del certificato tramite l'accesso keychain")](ad-hoc-distribution-images/createcertmanually02.png)

6. Dopo avere creato la richiesta di firma del certificato in base alle istruzioni, fare clic su Continue (Continua) e caricarla in Member Center:

  [ ![](ad-hoc-distribution-images/createcertmanually03.png "Caricare la richiesta di firma del certificato in Member Center")](ad-hoc-distribution-images/createcertmanually03.png)

7. Fare clic su Generate (Genera) per creare un certificato.
8. Scaricare infine il certificato completato e fare doppio clic sul file per installarlo.
9. A questo punto, il certificato deve essere installato nel computer, ma potrebbe essere necessario [aggiornare i profili](~/ios/get-started/installation/device-provisioning/manual-provisioning.md#download) per assicurarsi che siano visibili in Xcode.

In alternativa, è possibile richiedere un certificato tramite la finestra di dialogo Preferences (Preferenze) in Xcode. A tale scopo, seguire questa procedura:

1.   Selezionare il team e fare clic su **Manage Certificates…** (Gestisci certificati): [ ![](ad-hoc-distribution-images/selectteam.png "Selezione del team")](ad-hoc-distribution-images/selectteam.png)

2.   Fare quindi clic sul pulsante con il **segno più (+)** e selezionare **iOS App Store** (App Store iOS): [ ![](ad-hoc-distribution-images/selectcert.png "Selezione di iOS App Store (App Store iOS)")](ad-hoc-distribution-images/selectcert.png)

<a name="createprofile" />

## <a name="create-a-distribution-provisioning-profile"></a>Creare un profilo di provisioning di distribuzione

<a name="createappid" />

### <a name="create-an-app-id"></a>Creare un ID app
Come per qualsiasi altro profilo di provisioning creato, sarà necessario un ID app per identificare l'app che verrà distribuita nel dispositivo dell'utente. Se non è già stato creato, seguire questa procedura per crearne uno:


1. Nel [centro sviluppatori Apple](https://developer.apple.com/account/overview.action) passare alla sezione *Certificate, Identifiers and Profiles* (Certificati, identificatori e profili). Selezionare **App IDs** (ID app) in **Identifiers** (Identificatori).
2. Fare clic sul pulsante **+** e specificare un **nome** lo identificherà nel portale.
3. Il prefisso App sarà già impostato come ID del team e non può essere modificato. Selezionare un ID app esplicito o con caratteri jolly e immettere un ID bundle in un formato DNS inverso, ad esempio:
    - **Esplicito**: `com.[DomainName].[AppName]`
    - **Con caratteri jolly**: `com.[DomainName].*`
4. Selezionare il [servizio app](~/ios/get-started/installation/device-provisioning/manual-provisioning.md#appservices) richiesto dall'app.
5. Fare clic sul pulsante **Continue** (Continua) e seguire le istruzioni visualizzate sullo schermo per creare il nuovo ID app.

Quando i componenti necessari per la creazione di un profilo di distribuzione sono disponibili, seguire questi passaggi per crearne uno:

1. Tornare al portale di provisioning Apple e selezionare **Provisioning > Distribution** (Provisioning > Distribuzione): [ ![](ad-hoc-distribution-images/distribute01.png "Selezionare Provisioning > Distribution (Provisioning > Distribuzione)")](ad-hoc-distribution-images/distribute01.png)

2. Fare clic sul pulsante **+** e selezionare il tipo di profilo di distribuzione che si vuole creare come **Ad-Hoc** (Ad hoc):

    [ ![](ad-hoc-distribution-images/distribute02.png "Creare un tipo di distribuzione ad hoc")](ad-hoc-distribution-images/distribute02.png)

3. Fare clic sul pulsante **Continue** (Continua) e selezionare un ID app nell'elenco a discesa per il quale si vuole creare un profilo di distribuzione:

    [ ![](ad-hoc-distribution-images/distribute03.png "Selezionare un ID app dall'elenco a discesa")](ad-hoc-distribution-images/distribute03.png)

4. Fare clic sul pulsante **Continue** (Continua) e selezionare il certificato di distribuzione necessario per firmare l'applicazione:

    [ ![](ad-hoc-distribution-images/distribute04.png "Selezionare il certificato di distribuzione necessario per firmare l'applicazione")](ad-hoc-distribution-images/distribute04.png)

6. Fare clic sul pulsante **Continue** (Continua) e immettere un **nome** per il nuovo profilo di distribuzione:

    [ ![](ad-hoc-distribution-images/distribute06.png "Immettere un nome per il nuovo profilo di distribuzione")](ad-hoc-distribution-images/distribute06.png)

7. Fare clic sul pulsante **Generate** (Genera) per creare il nuovo profilo e finalizzare il processo.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

Prima che un nuovo profilo di distribuzione sia disponibile in Visual Studio per Mac, potrebbe essere necessario uscire da Visual Studio per Mac e aggiornare in Xcode l'elenco di identità di firma e di profili di provisioning disponibili (seguendo le istruzioni della sezione [Download di profili e certificati in Xcode](~/ios/get-started/installation/device-provisioning/manual-provisioning.md#download)).

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Prima che un nuovo profilo di distribuzione sia disponibile in Visual Studio, potrebbe essere necessario uscire da Visual Studio e aggiornare in Xcode (nel Mac dell'host di compilazione) l'elenco di identità di firma e di profili di provisioning disponibili (seguendo le istruzioni della sezione [Download di profili e certificati in Xcode](~/ios/get-started/installation/device-provisioning/manual-provisioning.md#download)).

-----

<a name="selectprofile" />

## <a name="selecting-a-distribution-profile-in-a-xamarinios-project"></a>Selezione di un profilo di distribuzione in un progetto Xamarin.iOS

Quando si è pronti per eseguire la compilazione finale di un'applicazione Xamarin.iOS, selezionare il profilo di distribuzione creato prima.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

 In Visual Studio per Mac seguire questa procedura:

1. Fare doppio clic sul nome del progetto in **Esplora soluzioni** per aprirlo e modificarlo.
2. Selezionare **Firma del bundle iOS** e il tipo di compilazione dall'elenco a discesa **Configurazione**:

    ![](ad-hoc-distribution-images/releasexs01.png "Selezionare il tipo di compilazione dall'elenco a discesa Configurazione")
3. Nella maggior parte dei casi, è possibile lasciare **Identità di firma** e **Profilo di provisioning** impostati sul valore predefinito **Automatico**. Visual Studio per Mac sceglierà il profilo corretto, in base all'Identificatore del bundle in Info.plist:

    ![](ad-hoc-distribution-images/releasexs02.png "Identità di firma e Profilo di provisioning impostati sul valore predefinito Automatico")
4. Se necessario, selezionare l'identità di firma e il profilo di distribuzione (quello creato prima) dagli elenchi a discesa:

    ![](ad-hoc-distribution-images/releasexs03.png "Selezionare l'identità di firma e il profilo di distribuzione")
5. Fare clic su **OK** per salvare le modifiche.

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)
 In Visual Studio seguire questa procedura:

1. In **Esplora soluzioni** fare clic con il pulsante destro del mouse sul nome del progetto e scegliere **Proprietà** per aprirle e modificarle.
2. Selezionare **Firma del bundle iOS** e il tipo di compilazione dall'elenco a discesa **Configurazione**:

    ![](ad-hoc-distribution-images/releasevs01.png "Selezionare il tipo di compilazione dall'elenco a discesa Configurazione")
3. Nella maggior parte dei casi, è possibile lasciare **Identità di firma** e **Profilo di provisioning** impostati sul valore predefinito **Automatico**. Visual Studio sceglierà il profilo corretto, in base all'Identificatore del bundle in Info.plist:

    ![](ad-hoc-distribution-images/releasevs02.png "Identità di firma e Profilo di provisioning impostati sul valore predefinito Automatico")
4. Se necessario, selezionare l'identità di firma e il profilo di distribuzione (quello creato prima) dagli elenchi a discesa:

    ![](ad-hoc-distribution-images/releasevs03.png "Selezionare l'identità di firma e il profilo di distribuzione")
5. Salvare le modifiche alle proprietà del progetto.

-----

<a name="adhoc" />

## <a name="ad-hoc-distribution"></a>Distribuzione ad hoc

[TestFlight](~/ios/deploy-test/testflight.md) è un noto metodo di test delle versioni beta e di distribuzione, ma, facendo parte di iTunes Connect, non è disponibile per i membri del **programma Apple Developer Enterprise**.

La distribuzione ad hoc consente agli sviluppatori di effettuare il test delle versioni beta delle app su un'ampia gamma di dispositivi quando iTunes Connect non è un'opzione. La distribuzione ad hoc è simile a quella interna. È necessario creare un file IPA, che sarà quindi possibile distribuire in modalità wireless o manualmente tramite iTunes.

<a name="IPA_Creation" />

### <a name="ipa-support-for-ad-hoc-deployment"></a>Supporto IPA per la distribuzione ad hoc

Dopo il provisioning, le applicazioni possono essere compresse in un file noto come *IPA*. Si tratta di un file ZIP contenente l'applicazione, con le icone e i metadati aggiuntivi. Il file IPA viene usato per aggiungere un'applicazione in locale in iTunes in modo che possa essere sincronizzata direttamente con un dispositivo incluso nel profilo di provisioning.

Per altre informazioni sulla creazione di un file IPA, vedere [Supporto IPA](~/ios/deploy-test/app-distribution/ipa-support.md).

## <a name="summary"></a>Riepilogo

Questo articolo ha illustrato i meccanismi di distribuzione ad hoc, necessari per il test delle applicazioni Xamarin.iOS.


## <a name="related-links"></a>Collegamenti correlati

- [Distribuzione tramite l'App Store](~/ios/deploy-test/app-distribution/app-store-distribution/index.md)
- [Distribuzione interna](~/ios/deploy-test/app-distribution/in-house-distribution.md)
- [File iTunesMetadata.plist](~/ios/deploy-test/app-distribution/itunesmetadata.md)
- [Supporto IPA](~/ios/deploy-test/app-distribution/ipa-support.md)
