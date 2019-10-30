---
title: Distribuzione interna per le app Xamarin.iOS
description: Questo documento offre una breve panoramica della distribuzione interna di applicazioni, come membro del programma Apple Enterprise Developer.
ms.prod: xamarin
ms.assetid: 9466E51E-303E-466E-85D7-D0525E16BB37
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/19/2017
ms.openlocfilehash: 9b9db3e6ae081a02d2b2297e70c216015dceade6
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73026542"
---
# <a name="in-house-distribution-for-xamarinios-apps"></a>Distribuzione interna per le app Xamarin.iOS

_Questo documento offre una breve panoramica della distribuzione interna di applicazioni, come membro del programma Apple Enterprise Developer._

Dopo che l'app Xamarin.iOS è stata sviluppata, il passaggio successivo del ciclo di vita dello sviluppo software è la distribuzione dell'app agli utenti. Le applicazioni proprietarie possono essere distribuite *internamente* (distribuzione definita in precedenza Enterprise) tramite il **programma Apple Developer Enterprise**, che offre i vantaggi seguenti:

- Non è necessario inviare l'applicazione ad Apple per la revisione.
- Non sono previsti limiti al numero di dispositivi in cui è possibile distribuire un'applicazione
  - È importante ricordare che Apple afferma chiaramente che le applicazioni interne sono solo per uso interno.

È anche importante tenere presente che il programma Enterprise:

- Non fornisce accesso a iTunes Connect per la distribuzione o il test (incluso TestFlight).
- Il costo dell'appartenenza è di 299 dollari all'anno.

È tuttavia necessario che tutte le app siano firmate da Apple.

<a name="testing" />

## <a name="testing-your-application"></a>Test dell'applicazione

Il test dell'applicazione viene eseguito con la distribuzione ad hoc. Per altre informazioni sul test, seguire la procedura illustrata nella guida [Distribuzione ad hoc](~/ios/deploy-test/app-distribution/ad-hoc-distribution.md). Tenere presente che è possibile testare solo un massimo di 100 dispositivi.

<a name="setup" />

## <a name="getting-set-up-for-distribution"></a>Configurazione per la distribuzione

Come negli altri programmi Apple Developer, nel programma Apple Developer Enterprise solo gli amministratori e gli agenti del team possono creare i certificati di distribuzione e i profili di provisioning.

I certificati del programma Apple Developer Enterprise saranno validi per tre anni e i profili di provisioning scadranno dopo un anno.

È importante tenere presente che i certificati scaduti non possono essere rinnovati, ma sarà necessario sostituire il certificato scaduto con uno nuovo, come illustrato in dettaglio [sotto](#certificate).

<a name="certificate" />

## <a name="creating-a-distribution-certificate"></a>Creazione di un certificato di distribuzione

1. Passare alla sezione *Certificates, Identifiers & Profiles* (Certificati, identificatori e profili) di Apple Developer Member Center.
2. In *Certificates* (Certificati) selezionare **Production** (Produzione).
3. Fare clic sul pulsante **+** per creare un nuovo certificato.
4. Nell'intestazione *Production* (Produzione) selezionare **App Store and Ad Hoc** (Interna e ad hoc):

   [![](in-house-distribution-images/createcertmanually01.png "Select In-House and Ad Hoc")](in-house-distribution-images/createcertmanually01.png#lightbox)

5. Fare clic su Continue (Continua) e seguire le istruzioni per creare una richiesta di firma del certificato tramite l'accesso keychain:

   [![](in-house-distribution-images/createcertmanually02.png "Create a Certificate Signing Request via Keychain Access")](in-house-distribution-images/createcertmanually02.png#lightbox)

6. Dopo avere creato la richiesta di firma del certificato in base alle istruzioni, fare clic su Continue (Continua) e caricarla in Member Center:

   [![](in-house-distribution-images/createcertmanually03.png "Upload the CSR to the Member Center")](in-house-distribution-images/createcertmanually03.png#lightbox)

7. Fare clic su Generate (Genera) per creare il certificato.
8. Scaricare il certificato completato e fare doppio clic sul file per installarlo.
9. A questo punto, il certificato deve essere installato nel computer, ma potrebbe essere necessario aggiornare i profili per assicurarsi che siano visibili in Xcode.

In alternativa, è possibile richiedere un certificato tramite la finestra di dialogo Preferences (Preferenze) in Xcode. A tale scopo, attenersi alla seguente procedura:

1. Selezionare il proprio team e fare clic su *View Details...* (Visualizza dettagli):

   [![](in-house-distribution-images/selectteam.png "Select your team")](in-house-distribution-images/selectteam.png#lightbox)

2. Fare quindi clic sul pulsante **Create** (Crea) accanto a **iOS Distribution Certificate** (Certificato di distribuzione iOS):

   [![](in-house-distribution-images/selectcert.png "Create the iOS Distribution Certificate")](in-house-distribution-images/selectcert.png#lightbox)

3. Fare quindi clic sul pulsante con il segno **più (+)** e selezionare **iOS App Store** (App Store iOS):

   [![](in-house-distribution-images/selectcert.png "Select iOS App Store")](in-house-distribution-images/selectcert.png#lightbox)

<a name="profile" />

## <a name="creating-a-distribution-provisioning-profile"></a>Creazione di un profilo di provisioning di distribuzione

<a name="appid" />

### <a name="creating-an-app-id"></a>Creazione di un ID app

Come per qualsiasi altro profilo di provisioning creato, sarà necessario un ID app per identificare l'app che si distribuirà nel dispositivo dell'utente. Se non è già stato creato, seguire questa procedura per crearne uno:

1. Nel [centro sviluppatori Apple](https://developer.apple.com/account/overview.action) passare alla sezione *Certificate, Identifiers and Profiles* (Certificati, identificatori e profili). Selezionare **App IDs** (ID app) in **Identifiers** (Identificatori).
2. Fare clic sul pulsante **+** e specificare un **nome** lo identificherà nel portale.
3. Il prefisso App sarà già impostato come ID del team e non può essere modificato. Selezionare un ID app esplicito o con caratteri jolly e immettere un ID bundle in un formato DNS inverso, ad esempio: **Esplicito**: com.[NomeDominio].[NomeApp] **Con caratteri jolly**: com.[NomeDominio].*
4. Selezionare il [servizio app](~/ios/get-started/installation/device-provisioning/index.md#provisioning-for-application-services) richiesto dall'app.
5. Fare clic sul pulsante **Continue** (Continua) e seguire le istruzioni visualizzate sullo schermo per creare il nuovo ID app.

Quando i componenti necessari per la creazione di un profilo di distribuzione sono disponibili, seguire questi passaggi per crearne uno:

1. Tornare al portale di provisioning Apple e selezionare **Provisioning** > **Distribution** (Distribuzione):

   [![](in-house-distribution-images/distribute01.png "Select Provisioning > Distribution")](in-house-distribution-images/distribute01.png#lightbox)

2. Fare clic sul pulsante **+** e selezionare il tipo di profilo di distribuzione che si vuole creare come **In-House** (Interna):

   [![](in-house-distribution-images/distribute02.png "Create an In-House Distribution Profile")](in-house-distribution-images/distribute02.png#lightbox)

3. Fare clic sul pulsante **Continue** (Continua) e selezionare un ID app nell'elenco a discesa per il quale si vuole creare un profilo di distribuzione:

   [![](in-house-distribution-images/distribute03.png "Select App ID from the dropdown list")](in-house-distribution-images/distribute03.png#lightbox)

4. Fare clic sul pulsante **Continue** (Continua) e selezionare il certificato di distribuzione necessario per firmare l'applicazione:

   [![](in-house-distribution-images/distribute04.png "Select distribution certificate required to sign the application")](in-house-distribution-images/distribute04.png#lightbox)

5. Fare clic sul pulsante **Continue** (Continua) e immettere un **nome** per il nuovo profilo di distribuzione:

   [![](in-house-distribution-images/distribute06.png "Enter a Name for the new Distribution Profile")](in-house-distribution-images/distribute06.png#lightbox)

6. Fare clic sul pulsante **Generate** (Genera) per creare il nuovo profilo e finalizzare il processo.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

 Prima che un nuovo profilo di distribuzione sia disponibile in Visual Studio per Mac, potrebbe essere necessario uscire da Visual Studio per Mac e aggiornare in Xcode l'elenco di identità di firma e di profili di provisioning disponibili, seguendo le istruzioni della sezione [Requesting Signing Identities](~/ios/get-started/installation/device-provisioning/manual-provisioning.md#download) (Richiesta di identità di firma).

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Prima che un nuovo profilo di distribuzione sia disponibile in Visual Studio, potrebbe essere necessario uscire da Visual Studio e aggiornare in Xcode (nel Mac dell'host di compilazione) l'elenco di identità di firma e di profili di provisioning disponibili, seguendo le istruzioni della sezione [Requesting Signing Identities](~/ios/get-started/installation/device-provisioning/manual-provisioning.md#download) (Richiesta di identità di firma).

-----

<a name="inhouse" />

## <a name="distributing-your-app-in-house"></a>Distribuzione interna dell'app

Nel programma Apple Developer Enterprise, il licenziatario è la persona responsabile della distribuzione dell'applicazione e dell'adozione delle [linee guida](https://developer.apple.com/programs/enterprise/) stabilite da Apple.

È possibile distribuire l'app in modo sicuro con diversi mezzi:

- Localmente tramite iTunes
- Server MDM
- Un server Web sicuro interno
- Email

Per distribuire l'app in uno di questi modi, è prima necessario creare un file IPA, come illustrato nella sezione successiva.

### <a name="creating-an-ipa-for-in-house-deployment"></a>Creazione di un file IPA per la distribuzione interna

Dopo il provisioning, le applicazioni possono essere compresse in un file noto come *IPA*. Si tratta di un file ZIP contenente l'applicazione, con le icone e i metadati aggiuntivi. Il file IPA viene usato per aggiungere un'applicazione in locale in iTunes in modo che possa essere sincronizzata direttamente con un dispositivo incluso nel profilo di provisioning.

Per altre informazioni sulla creazione di un file IPA, vedere [Supporto IPA](~/ios/deploy-test/app-distribution/ipa-support.md).

## <a name="summary"></a>Riepilogo

Questo articolo ha offerto una breve panoramica della distribuzione di applicazioni Xamarin.iOS interna.

## <a name="related-links"></a>Collegamenti correlati

- [Distribuzione tramite l'App Store](~/ios/deploy-test/app-distribution/app-store-distribution/index.md)
- [Distribuzione ad hoc](~/ios/deploy-test/app-distribution/ad-hoc-distribution.md)
- [File iTunesMetadata.plist](~/ios/deploy-test/app-distribution/itunesmetadata.md)
- [Supporto IPA](~/ios/deploy-test/app-distribution/ipa-support.md)
