---
title: Distribuzione tramite l'App Store
description: Questo documento descrive come distribuire un'applicazione Xamarin.iOS nell'App Store. Viene illustrato come creare un certificato di distribuzione, come creare un profilo di provisioning di distribuzione e come configurare iTunes Connect e inviare l'app.
ms.prod: xamarin
ms.assetid: B07E2C1F-A6DF-43CB-BFB0-0252A5558467
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 08/23/2017
ms.openlocfilehash: 73c4e992fae1a2d525670604d98f277fe4fde794
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2020
ms.locfileid: "79304233"
---
# <a name="app-store-distribution"></a>Distribuzione tramite l'App Store

Dopo che un'app Xamarin.iOS è stata sviluppata, il passaggio successivo del ciclo di vita dello sviluppo software è la distribuzione dell'app agli utenti tramite l'App Store iTunes. Questa è la modalità di distribuzione più comune per le applicazioni. Pubblicando un'applicazione nell'App Store di Apple, è possibile renderla disponibile per gli utenti di tutto il mondo.

> [!IMPORTANT]
> Apple [ha indicato](https://developer.apple.com/ios/submit/) che a partire da marzo 2019 tutte le app e gli aggiornamenti inviati all'App Store devono essere stati compilati con iOS 12.1 SDK o versioni successive, disponibili in Xcode 10.1 o versioni successive.
> Le app devono anche supportare iPhone Xs e le dimensioni dello schermo 12,9" di iPad Pro.

> [!IMPORTANT]
> Se si cercano risorse relative `UIWebView` all'avviso di deprecazione (ITMS-90809) durante l'utilizzo di Xamarin.Forms, fare riferimento alla documentazione di [Xamarin.Forms WebView.](~/xamarin-forms/user-interface/webview.md#uiwebview-deprecation-and-app-store-rejection-itms-90809)

Per distribuire un'applicazione, come per sviluppare un'applicazione, è necessario effettuare il provisioning dell'applicazione con il *profilo di provisioning* appropriato. I profili di provisioning sono file che contengono le informazioni sulla firma del codice, oltre all'identità dell'applicazione e al meccanismo di distribuzione previsto. Contengono anche informazioni sui dispositivi in cui l'app può essere distribuita per la distribuzione senza App Store.

> [!IMPORTANT]
> Per usare iTunes Connect e quindi per pubblicare un'app nell'App Store, è **necessario** fare parte di un programma Apple Developer per singoli utenti o per organizzazioni. Non sarà possibile eseguire i passaggi di questa pagina se si è membri di un programma Apple Developer **Enterprise**.

<a name="provisioning" />

## <a name="provisioning-an-app-for-app-store-distribution"></a>Provisioning di un'app per la distribuzione nell'App Store

Indipendentemente da come si prevede di rilasciare un'applicazione Xamarin.iOS, sarà necessario compilare un *profilo di provisioning di distribuzione* specifico. Questo profilo consente di applicare una firma digitale all'applicazione per il rilascio in modo che possa essere installata in un dispositivo iOS. Analogamente a un profilo di provisioning di sviluppo, un profilo di distribuzione conterrà quanto segue:

- Un ID app
- Un certificato di distribuzione

È possibile selezionare lo stesso **ID app** e gli stessi **dispositivi** usati per il profilo di provisioning di sviluppo, ma, se non ne è già disponibile uno, sarà necessario creare un certificato di distribuzione per identificare l'organizzazione quando si invia l'app all'App Store. La procedura di creazione di un certificato di distribuzione è descritta nella sezione successiva.

> [!NOTE]
> Solo gli agenti e gli amministratori del team possono creare i certificati di distribuzione e i profili di provisioning.

<a name="creatingcertificate" />

## <a name="creating-a-distribution-certificate"></a>Creazione di un certificato di distribuzione

1. Passare alla sezione *Certificates, Identifiers & Profiles* (Certificati, identificatori e profili) di Apple Developer Member Center.
2. In *Certificates* (Certificati) selezionare **Production** (Produzione).
3. Fare **+** clic sul pulsante per creare un nuovo certificato.
4. Nell'intestazione *Production* (Produzione) selezionare **App Store and Ad Hoc** (App Store e ad hoc):

    [![](images/createcertmanually01.png "Select App Store and Ad Hoc")](images/createcertmanually01.png#lightbox)
5. Fare clic su **Continua**e seguire le istruzioni per creare una richiesta di firma del certificato tramite Accesso Portachiavi:

    [![](images/createcertmanually02.png "Create a Certificate Signing Request via Keychain Access")](images/createcertmanually02.png#lightbox)
6. Una volta creata la CSR come indicato, fare clic su **Continua**e caricare la CSR nel Centro membri:

    [![](images/createcertmanually03.png "Upload the CSR to the Member Center")](images/createcertmanually03.png#lightbox)

7. Fare clic su **Generate** (Genera) per creare il certificato.
8. Infine, **scaricare** il certificato completato e fare doppio clic sul file per installarlo.
9. A questo punto, il certificato deve essere installato nel computer, ma potrebbe essere necessario [aggiornare i profili](~/ios/get-started/installation/device-provisioning/manual-provisioning.md#download)per assicurarsi che siano visibili in Xcode.

In alternativa, è possibile richiedere un certificato tramite la finestra di dialogo Preferences (Preferenze) in Xcode. A questo scopo, eseguire la procedura seguente.

1. Selezionare il team e fare clic su **Gestisci certificati...**:[![](images/selectteam.png "Selezionare il team e View Details (Visualizza dettagli)")](images/selectteam.png#lightbox)

2. Successivamente, fare clic sul pulsante **Crea** accanto a **Certificato di distribuzione iOS**:[![](images/selectcert.png "Creare un certificato di distribuzione iOS")](images/selectcert.png#lightbox)

3. A seconda dei privilegi del team, verrà generata l'identità di firma, come illustrato di seguito, oppure potrebbe essere necessario attendere che un agente del team o un amministratore la approvi:[![](images/generated.png "L'identità di firma verrà generata e verrà visualizzata una finestra di dialogo")](images/generated.png#lightbox)

<a name="creatingprofile" />

## <a name="creating-a-distribution-profile"></a>Creazione di un profilo di distribuzione

<a name="creatingappid" />

### <a name="creating-an-app-id"></a>Creazione di un ID app

Come per qualsiasi altro profilo di provisioning creato, è necessario un ID app per identificare l'app che viene distribuita nel dispositivo dell'utente. Se non è già stato creato, seguire questa procedura per crearne uno:

1. Nel [centro sviluppatori Apple](https://developer.apple.com/account/overview.action) passare alla sezione *Certificate, Identifiers and Profiles* (Certificati, identificatori e profili). Selezionare **App IDs** (ID app) in **Identifiers** (Identificatori).
2. Fare **+** clic sul pulsante e fornire un **Nome** che lo identificherà nel portale.
3. Il prefisso App sarà già impostato come ID del team e non può essere modificato. Selezionare un ID app esplicito o con caratteri jolly e immettere un ID bundle in un formato DNS inverso, ad esempio:
    - **Esplicito**: com.[NomeDominio].[NomeApp]
    - **Con caratteri jolly**:com.[NomeDominio].*
4. Selezionare il [servizio app](~/ios/get-started/installation/device-provisioning/manual-provisioning.md#provisioning-for-application-services) richiesto dall'app.
5. Fare clic sul pulsante **Continue** (Continua) e seguire le istruzioni visualizzate sullo schermo per creare il nuovo ID app.

### <a name="creating-a-provisioning-profile"></a>Creazione di un profilo di provisioning

Quando i componenti necessari per la creazione di un profilo di distribuzione sono disponibili, seguire questi passaggi per crearne uno:

1. Torna al portale di provisioning Apple e seleziona**Distribuzione** **provisioning** > :

    [![](images/distribute01.png "RSelect Provisioning > Distribution")](images/distribute01.png#lightbox)

2. Fai **+** clic sul pulsante e seleziona il tipo di profilo di distribuzione che desideri creare come **App Store:**

    [![](images/distribute02.png "Create an App Store distribution profile")](images/distribute02.png#lightbox)

3. Fare clic sul pulsante **Continue** (Continua) e selezionare un ID app nell'elenco a discesa per il quale si vuole creare un profilo di distribuzione:

    [![](images/distribute03.png "Select App ID from the dropdown list")](images/distribute03.png#lightbox)

4. Fare clic sul pulsante **Continue** (Continua) e selezionare il certificato necessario per firmare l'applicazione:

    [![](images/distribute04.png "Select the certificate required to sign the application")](images/distribute04.png#lightbox)

5. Fare clic sul pulsante **Continue** (Continua) e selezionare i dispositivi iOS in cui sarà consentito eseguire l'applicazione Xamarin.iOS:

    [![](images/distribute05.png "Select the iOS devices that app will be allowed to run on")](images/distribute05.png#lightbox)

6. Fare clic sul pulsante **Continue** (Continua) e immettere un **nome** per il nuovo profilo di distribuzione:

    [![](images/distribute06.png "Enter a Name for the new Distribution Profile")](images/distribute06.png#lightbox)

7. Fare clic sul pulsante **Generate** (Genera) per creare il nuovo profilo e finalizzare il processo.

# <a name="visual-studio-for-mac"></a>[Visual Studio per Mac](#tab/macos)

 Prima che un nuovo profilo di distribuzione sia disponibile in Visual Studio per Mac, potrebbe essere necessario uscire da Visual Studio per Mac e aggiornare in Xcode l'elenco di identità di firma e di profili di provisioning disponibili, seguendo le istruzioni della sezione [Requesting Signing Identities](~/ios/get-started/installation/device-provisioning/manual-provisioning.md#download) (Richiesta di identità di firma).

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

 Potrebbe essere necessario chiudere Visual Studio e fare in modo che Xcode (nel Mac dell'host di compilazione) aggiorni l'elenco delle identità di firma e dei profili di provisioning disponibili (seguendo le istruzioni nella sezione Richiesta di identità di [firma)](~/ios/get-started/installation/device-provisioning/manual-provisioning.md#download) prima che un nuovo profilo di distribuzione sia disponibile in Visual Studio.

-----

<a name="selectprofile" />

## <a name="selecting-a-distribution-profile-in-a-xamarinios-project"></a>Selezione di un profilo di distribuzione in un progetto Xamarin.iOS

Quando si è pronti per eseguire la compilazione finale di un'applicazione Xamarin.iOS per venderla nell'App Store iTunes, selezionare il profilo di distribuzione creato prima.

# <a name="visual-studio-for-mac"></a>[Visual Studio per Mac](#tab/macos)

 In Visual Studio per Mac seguire questa procedura:

1. Fare doppio clic sul nome del progetto in **Esplora soluzioni** per aprirlo e modificarlo.
2. Selezionare **Firma del bundle iOS** e **Versione | iPhone** dall'elenco a discesa **Configurazione**:

    ![](images/releasexs01.png "Select Release | iPhone from the Configuration dropdown")
3. Nella maggior parte dei casi, è possibile lasciare **Identità di firma** e **Profilo di provisioning** impostati sul valore predefinito **Automatico**. Visual Studio per Mac sceglierà il profilo corretto, in base all'Identificatore del bundle in Info.plist:

    ![](images/releasexs02.png "The Signing Identity and the Provisioning Profile set to the default values of Automatic")
4. Se necessario, selezionare l'identità di firma e il profilo di distribuzione (quello creato prima) dagli elenchi a discesa:

    ![](images/releasexs03.png "Select the Signing Identity and Distribution Profiles")
5. Fare clic su **OK** per salvare le modifiche.

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

 In Visual Studio eseguire le operazioni seguenti:

1. In **Esplora soluzioni** fare clic con il pulsante destro del mouse sul nome del progetto e scegliere **Proprietà** per aprirle e modificarle.
2. Selezionare **Firma del bundle iOS** e **Versione | iPhone** dall'elenco a discesa **Configurazione**:

    ![](images/releasevs01.png "Select Release | iPhone from the Configuration dropdown")
3. Nella maggior parte dei casi, è possibile lasciare **Identità di firma** e **Profilo di provisioning** impostati sul valore predefinito **Automatico**. Visual Studio sceglierà il profilo corretto, in base all'Identificatore del bundle in Info.plist

    ![](images/releasevs02.png "The Signing Identity and the Provisioning Profile set to the default values of Automatic")
4. Se necessario, selezionare l'identità di firma e il profilo di distribuzione (quello creato prima) dagli elenchi a discesa:

    ![](images/releasevs03.png "Select the Signing Identity and Distribution Profile")
5. Salvare le modifiche alle proprietà del progetto.

-----

<a name="itunesconnect" />

## <a name="configuring-your-application-in-itunes-connect"></a>Configurazione dell'applicazione in iTunes Connect

Dopo il corretto provisioning dell'applicazione, il passaggio successivo prevede la configurazione delle app in [iTunes Connect](https://itunesconnect.apple.com/WebObjects/iTunesConnect.woa), una suite di strumenti basati sul Web che consentono, a titolo di esempio, di gestire le applicazioni iOS nell'App Store.

È necessario che l'applicazione Xamarin.iOS sia correttamente impostata e configurata in iTunes Connect prima di poter essere inviata ad Apple per la revisione e, infine, rilasciata come app gratuita o in vendita in App Store.

Per altri dettagli, vedere la documentazione [Configuring an App in iTunes Connect](~/ios/deploy-test/app-distribution/app-store-distribution/itunesconnect.md) (Configurazione di un'app in iTunes Connect).

<a name="submitting" />

## <a name="submitting-an-app-to-itunes-connect"></a>Invio di un'app a iTunes Connect

Dopo la firma dell'applicazione con il profilo di provisioning di distribuzione e la creazione dell'app in iTunes Connect, il file binario dell'applicazione viene caricato in Apple per la revisione. Se la revisione di Apple ha esito positivo, viene resa disponibile nell'App Store.

Per altre informazioni sulla pubblicazione di applicazioni nell'App Store, vedere [Pubblicazione in App Store](~/ios/deploy-test/app-distribution/app-store-distribution/publishing-to-the-app-store.md).

<a name="windows" />

## <a name="automatically-copy-app-bundles-back-to-windows"></a>Copiare automaticamente i bundle delle app nuovamente in Windows

[!include[](~/ios/includes/copy-app-bundle-to-windows.md)]

## <a name="summary"></a>Riepilogo

Questo articolo ha illustrato i componenti chiave della preparazione di un'applicazione Xamarin.iOS per la distribuzione nell'App Store.

## <a name="related-links"></a>Collegamenti correlati

- [Configurazione di un'app in iTunes Connect](~/ios/deploy-test/app-distribution/app-store-distribution/itunesconnect.md)
- [Pubblicazione nell'App Store](~/ios/deploy-test/app-distribution/app-store-distribution/publishing-to-the-app-store.md)
- [Distribuzione interna](~/ios/deploy-test/app-distribution/in-house-distribution.md)
- [Distribuzione ad hoc](~/ios/deploy-test/app-distribution/ad-hoc-distribution.md)
- [Il file iTunesMetadata.plist](~/ios/deploy-test/app-distribution/itunesmetadata.md)
- [Supporto IPA](~/ios/deploy-test/app-distribution/ipa-support.md)
