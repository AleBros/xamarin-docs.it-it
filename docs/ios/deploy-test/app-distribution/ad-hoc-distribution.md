---
title: Distribuzione ad hoc per le app Xamarin.iOS
description: Questo documento offre una panoramica delle tecniche di distribuzione ad hoc usate principalmente per testare le applicazioni Xamarin.iOS con un gruppo numeroso di persone.
ms.prod: xamarin
ms.assetid: 3B621CAD-103C-478A-97C3-829015F48D1A
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/19/2017
ms.openlocfilehash: 16ae440ec2fe3b5898c8f92d993279ef5e7e9794
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2020
ms.locfileid: "75607919"
---
# <a name="ad-hoc-distribution-for-xamarinios-apps"></a>Distribuzione ad hoc per le app Xamarin.iOS

_Questo documento offre una panoramica delle tecniche di distribuzione ad hoc usate principalmente per testare le applicazioni Xamarin.iOS con un gruppo numeroso di persone._

Dopo che un'app Xamarin.iOS è stata sviluppata, il passaggio successivo del ciclo di vita dello sviluppo software è la distribuzione dell'app agli utenti per il test.

iTunes Connect è una delle opzioni per gestire il test dell'app, che viene descritta più dettagliatamente nella guida a [TestFlight](~/ios/deploy-test/testflight.md). I membri del programma Apple Developer Enterprise non hanno tuttavia accesso a iTunes Connect, quindi la distribuzione *ad hoc* è il metodo migliore per testare queste app.

Le applicazioni Xamarin.iOS possono essere testate dall'utente tramite distribuzione *ad hoc,* disponibile sia sull'Apple Developer Program che sull'Apple Developer Enterprise Program, e consente di testare fino a 100 dispositivi iOS.

La distribuzione ad hoc ha il vantaggio di non richiedere l'approvazione dell'App Store e di poter essere installata in modalità wireless da un server Web o tramite iTunes. È tuttavia limitata a **100** dispositivi per ogni anno di appartenenza, sia per lo sviluppo che per la distribuzione, e i dispositivi devono essere aggiunti manualmente nel Member Center usando il valore UDID. Per altre informazioni sull'aggiunta di dispositivi, vedere la guida [Device Provisioning](~/ios/get-started/installation/device-provisioning/manual-provisioning.md#adddevice) (Provisioning di dispositivi).

Per la distribuzione ad hoc, è necessario effettuare il provisioning delle applicazioni usando un *profilo di provisioning* ad hoc contenente le informazioni sulla firma del codice, oltre all'identità dell'applicazione e ai dispositivi che possono installare l'applicazione.

Questa guida fornisce informazioni sul provisioning per la distribuzione ad hoc e su come distribuire un'app Xamarin.iOS.

<a name="setup" />

## <a name="setting-up-for-distribution"></a>Configurazione per la distribuzione

Anche se si prevede di rilasciare un'applicazione Xamarin.iOS per la distribuzione interna, a scopo di testing, sarà necessario compilare un profilo di provisioning per la distribuzione ad hoc specifico. Questo profilo consente di applicare una firma digitale a un'applicazione per il rilascio in modo che possa essere installata in un dispositivo iOS.

La sezione successiva illustra come eseguire la configurazione con un certificato di distribuzione e un profilo di provisioning di distribuzione.

> [!NOTE]
> Solo gli agenti e gli amministratori del team possono creare i certificati di distribuzione e i profili di provisioning.

<a name="createcertificate" />

## <a name="create-a-distribution-certificate"></a>Creare un certificato di distribuzione

1. Passare alla sezione *Certificates, Identifiers & Profiles* (Certificati, identificatori e profili) di Apple Developer Member Center.
2. In *Certificates* (Certificati) selezionare **Production** (Produzione).
3. Fare **+** clic sul pulsante per creare un nuovo certificato.
4. Nell'intestazione *Production* (Produzione) selezionare **In-House and Ad Hoc** (Interna e ad hoc) o **App Store and Ad Hoc** (App Store e ad hoc), in base all'appartenenza al programma:

   [![Seleziona In-House e Ad Hoc, o App Store e Ad Hoc](ad-hoc-distribution-images/cert-first-small.png)](ad-hoc-distribution-images/cert-first-large.png#lightbox)

5. Fare clic su Continue (Continua) e seguire le istruzioni per creare una richiesta di firma del certificato tramite l'accesso keychain:

   [![Creare una richiesta di firma del certificato tramite Accesso PortachiaviCreate a Certificate Signing Request via Keychain Access](ad-hoc-distribution-images/createcertmanually02.png)](ad-hoc-distribution-images/createcertmanually02.png#lightbox)

6. Dopo avere creato la richiesta di firma del certificato in base alle istruzioni, fare clic su Continue (Continua) e caricarla in Member Center:

   [![Caricare la CSR nel Centro membri](ad-hoc-distribution-images/createcertmanually03.png)](ad-hoc-distribution-images/createcertmanually03.png#lightbox)

7. Fare clic su Generate (Genera) per creare un certificato.
8. Scaricare infine il certificato completato e fare doppio clic sul file per installarlo.
9. A questo punto, il certificato deve essere installato nel computer, ma potrebbe essere necessario [aggiornare i profili](~/ios/get-started/installation/device-provisioning/manual-provisioning.md#download) per assicurarsi che siano visibili in Xcode.

In alternativa, è possibile richiedere un certificato tramite la finestra di dialogo Preferences (Preferenze) in Xcode. A questo scopo, eseguire la procedura seguente.

1. Seleziona il tuo team e [ ![](ad-hoc-distribution-images/selectteam.png)](ad-hoc-distribution-images/selectteam.png#lightbox) fai clic su Gestisci **certificati...**: Selezione del team

2. Successivamente, fai clic sul pulsante **più (-)** e seleziona **iOS App Store**: [ ![Selezione dell'App Store iOS](ad-hoc-distribution-images/selectcert.png)](ad-hoc-distribution-images/selectcert.png#lightbox)

<a name="createprofile" />

## <a name="create-a-distribution-provisioning-profile"></a>Creare un profilo di provisioning di distribuzione

<a name="createappid" />

### <a name="create-an-app-id"></a>Creare un'ID app
Come per qualsiasi altro profilo di provisioning creato, sarà necessario un ID app per identificare l'app che verrà distribuita nel dispositivo dell'utente. Se non è già stato creato, seguire questa procedura per crearne uno:

1. Nel [centro sviluppatori Apple](https://developer.apple.com/account/overview.action) passare alla sezione *Certificate, Identifiers and Profiles* (Certificati, identificatori e profili). Selezionare **App IDs** (ID app) in **Identifiers** (Identificatori).
2. Fare **+** clic sul pulsante e fornire un **Nome** che lo identificherà nel portale.
3. Il prefisso App sarà già impostato come ID del team e non può essere modificato. Selezionare un ID app esplicito o con caratteri jolly e immettere un ID bundle in un formato DNS inverso, ad esempio:
    - **Esplicito**:`com.[DomainName].[AppName]`
    - **Carattere jolly**:`com.[DomainName].*`
4. Selezionare il [servizio app](~/ios/get-started/installation/device-provisioning/manual-provisioning.md#provisioning-for-application-services) richiesto dall'app.
5. Fare clic sul pulsante **Continue** (Continua) e seguire le istruzioni visualizzate sullo schermo per creare il nuovo ID app.

Quando i componenti necessari per la creazione di un profilo di distribuzione sono disponibili, seguire questi passaggi per crearne uno:

1. Torna al portale di provisioning Apple e seleziona **Provisioning > distribuzione**: [ ![Seleziona Provisioning > distribuzione](ad-hoc-distribution-images/distribute01.png)](ad-hoc-distribution-images/distribute01.png#lightbox)

2. Fare **+** clic sul pulsante e selezionare il tipo di profilo di distribuzione che si desidera creare come **Ad-Hoc**:

    [![Creare un tipo di distribuzione ad hocCreate an Ad-Hoc Distribution type](ad-hoc-distribution-images/distribute02.png)](ad-hoc-distribution-images/distribute02.png#lightbox)

3. Fare clic sul pulsante **Continue** (Continua) e selezionare un ID app nell'elenco a discesa per il quale si vuole creare un profilo di distribuzione:

    [![Selezionare ID app dall'elenco a discesa](ad-hoc-distribution-images/distribute03.png)](ad-hoc-distribution-images/distribute03.png#lightbox)

4. Fare clic sul pulsante **Continue** (Continua) e selezionare il certificato di distribuzione necessario per firmare l'applicazione:

    [![Selezionare il certificato di distribuzione necessario per firmare l'applicazioneSelect distribution certificate required to sign the application](ad-hoc-distribution-images/distribute04.png)](ad-hoc-distribution-images/distribute04.png#lightbox)

5. Fare clic sul pulsante **Continue** (Continua) e immettere un **nome** per il nuovo profilo di distribuzione:

    [![Immettere un Nome per il nuovo profilo di distribuzione](ad-hoc-distribution-images/distribute06.png)](ad-hoc-distribution-images/distribute06.png#lightbox)

6. Fare clic sul pulsante **Generate** (Genera) per creare il nuovo profilo e finalizzare il processo.

# <a name="visual-studio-for-mac"></a>[Visual Studio per Mac](#tab/macos)

Prima che un nuovo profilo di distribuzione sia disponibile in Visual Studio per Mac, potrebbe essere necessario uscire da Visual Studio per Mac e aggiornare in Xcode l'elenco di identità di firma e di profili di provisioning disponibili (seguendo le istruzioni della sezione [Download di profili e certificati in Xcode](~/ios/get-started/installation/device-provisioning/manual-provisioning.md#download)).

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

Prima che un nuovo profilo di distribuzione sia disponibile in Visual Studio, potrebbe essere necessario uscire da Visual Studio e aggiornare in Xcode (nel Mac dell'host di compilazione) l'elenco di identità di firma e di profili di provisioning disponibili (seguendo le istruzioni della sezione [Download di profili e certificati in Xcode](~/ios/get-started/installation/device-provisioning/manual-provisioning.md#download)).

-----

<a name="selectprofile" />

## <a name="selecting-a-distribution-profile-in-a-xamarinios-project"></a>Selezione di un profilo di distribuzione in un progetto Xamarin.iOS

Quando si è pronti per eseguire la compilazione finale di un'applicazione Xamarin.iOS, selezionare il profilo di distribuzione creato prima.

# <a name="visual-studio-for-mac"></a>[Visual Studio per Mac](#tab/macos)

 In Visual Studio per Mac seguire questa procedura:

1. Fare doppio clic sul nome del progetto in **Esplora soluzioni** per aprirlo e modificarlo.
2. Selezionare **Firma del bundle iOS** e il tipo di compilazione dall'elenco a discesa **Configurazione**:

    ![Selezionare il tipo di compilazione dall'elenco a discesa Configurazione](ad-hoc-distribution-images/releasexs01.png)
3. Nella maggior parte dei casi, è possibile lasciare **Identità di firma** e **Profilo di provisioning** impostati sul valore predefinito **Automatico**. Visual Studio per Mac sceglierà il profilo corretto, in base all'Identificatore del bundle in Info.plist:

    ![Identità di firma e Profilo di provisioning impostati sul valore predefinito Automatico](ad-hoc-distribution-images/releasexs02.png)
4. Se necessario, selezionare l'identità di firma e il profilo di distribuzione (quello creato prima) dagli elenchi a discesa:

    ![Selezionare l'identità di firma e il profilo di distribuzione](ad-hoc-distribution-images/releasexs03.png)
5. Fare clic su **OK** per salvare le modifiche.

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)
 In Visual Studio eseguire le operazioni seguenti:

1. In **Esplora soluzioni** fare clic con il pulsante destro del mouse sul nome del progetto e scegliere **Proprietà** per aprirle e modificarle.
2. Selezionare **Firma del bundle iOS** e il tipo di compilazione dall'elenco a discesa **Configurazione**:

    ![Selezionare il tipo di compilazione dall'elenco a discesa Configurazione](ad-hoc-distribution-images/releasevs01.png)
3. Nella maggior parte dei casi, è possibile lasciare **Identità di firma** e **Profilo di provisioning** impostati sul valore predefinito **Automatico**. Visual Studio sceglierà il profilo corretto, in base all'Identificatore del bundle in Info.plist:

    ![Identità di firma e Profilo di provisioning impostati sul valore predefinito Automatico](ad-hoc-distribution-images/releasevs02.png)
4. Se necessario, selezionare l'identità di firma e il profilo di distribuzione (quello creato prima) dagli elenchi a discesa:

    ![Selezionare l'identità di firma e il profilo di distribuzione](ad-hoc-distribution-images/releasevs03.png)
5. Salvare le modifiche alle proprietà del progetto.

-----

<a name="adhoc" />

## <a name="ad-hoc-distribution"></a>Distribuzione ad hoc

[TestFlight](~/ios/deploy-test/testflight.md) è un noto metodo di test delle versioni beta e di distribuzione, ma, facendo parte di iTunes Connect, non è disponibile per i membri del **programma Apple Developer Enterprise**.

La distribuzione ad hoc consente agli sviluppatori di effettuare il test delle versioni beta delle app su un'ampia gamma di dispositivi quando iTunes Connect non è un'opzione. La distribuzione ad hoc è simile a quella interna. È necessario creare un file IPA, che sarà quindi possibile distribuire in modalità wireless o manualmente tramite iTunes.

<a name="IPA_Creation" />

### <a name="ipa-support-for-ad-hoc-deployment"></a>Supporto IPA per la distribuzione ad hoc

Dopo il provisioning, le applicazioni possono essere compresse in un file noto come *IPA*. Si tratta di un file ZIP contenente l'applicazione, con le icone e i metadati aggiuntivi. Il file IPA viene usato per aggiungere un'applicazione in locale in iTunes in modo che possa essere sincronizzata direttamente con un dispositivo incluso nel profilo di provisioning.

Per ulteriori informazioni sulla creazione di un IPA, vedere la Guida [al supporto IPA.](~/ios/deploy-test/app-distribution/ipa-support.md)

## <a name="summary"></a>Riepilogo

Questo articolo ha illustrato i meccanismi di distribuzione ad hoc, necessari per il test delle applicazioni Xamarin.iOS.

## <a name="related-links"></a>Collegamenti correlati

- [Distribuzione dell'App Store](~/ios/deploy-test/app-distribution/app-store-distribution/index.md)
- [Distribuzione interna](~/ios/deploy-test/app-distribution/in-house-distribution.md)
- [Il file iTunesMetadata.plist](~/ios/deploy-test/app-distribution/itunesmetadata.md)
- [Supporto IPA](~/ios/deploy-test/app-distribution/ipa-support.md)
