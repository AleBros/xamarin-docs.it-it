---
title: Provisioning gratuito per le app Xamarin.iOS
description: Questo documento descrive in che modo gli sviluppatori Xamarin.iOS possono testare le app in un dispositivo fisico senza doversi iscrivere al programma Apple Developer a pagamento.
ms.prod: xamarin
ms.assetid: A5CE2ECF-8057-49ED-8393-EB0C5977FE4C
ms.technology: xamarin-ios
author: asb3993
ms.author: amburns
ms.date: 07/16/2018
ms.openlocfilehash: 22ac17e211562eccbc49cc213e06079e77dd08c0
ms.sourcegitcommit: e98a9ce8b716796f15de7cec8c9465c4b6bb2997
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/18/2018
ms.locfileid: "39111157"
---
# <a name="free-provisioning-for-xamarinios-apps"></a>Provisioning gratuito per le app Xamarin.iOS

Il provisioning gratuito consente agli sviluppatori di Xamarin.iOS di distribuire e testare le proprie applicazioni nei dispositivi iOS **senza** far parte del **programma Apple Developer**.
Anche se i test del simulatore sono validi e pratici, è fondamentale testare le app nei dispositivi iOS fisici per verificare che funzionino correttamente con i vincoli di connettività di rete, archiviazione e memoria reali.

Per usare il provisioning gratuito per distribuire un'app in un dispositivo:

- Usare Xcode per creare l'*identità di firma* necessaria (certificato di sviluppo e chiave privata) e il *profilo di provisioning* contenente un ID app esplicito e il valore UDID del dispositivo iOS connesso.
- Usare l'identità di firma e il profilo di provisioning creati da Xcode in Visual Studio per Mac o Visual Studio 2017 per distribuire l'applicazione Xamarin.iOS.

> [!IMPORTANT]
> Il [provisioning automatico](~/ios/get-started/installation/device-provisioning/automatic-provisioning.md) consente a Visual Studio per Mac o Visual Studio 2017 di configurare automaticamente un dispositivo per i test di sviluppo. Tuttavia, il provisioning automatico non è compatibile con il provisioning gratuito. Per usare il provisioning automatico, è necessario avere un account del programma Apple Developer a pagamento.

## <a name="requirements"></a>Requisiti

Per distribuire le applicazioni Xamarin.iOS in un dispositivo con il provisioning gratuito:

- L'ID Apple usato allo scopo non deve essere connesso al programma Apple Developer.
- L'app Xamarin.iOS deve usare un ID app esplicito, non un ID app con carattere jolly.
- L'identificatore del bundle usato nell'app Xamarin.iOS deve essere univoco e non deve essere stato usato in un'altra app in precedenza. Gli identificatori bundle usati con il provisioning gratuito **non** possono essere riutilizzati.
- Se un'app è già stata distribuita, non è possibile implementarla con il provisioning gratuito.
- Se l'app usa i Servizi app, è necessario creare un profilo di provisioning, come descritto in dettaglio nella guida [Provisioning di dispositivi](~/ios/get-started/installation/device-provisioning/index.md#appservices). 

Vedere la sezione [Limitazioni](#limitations) di questo documento per altre informazioni sulle limitazioni associate al provisioning gratuito e fare riferimento a [Panoramica della distribuzione di app Xamarin.iOS](~/ios/deploy-test/app-distribution/index.md) per altre informazioni sulla distribuzione di applicazioni iOS.

## <a name="testing-on-device-with-free-provisioning"></a>Test su dispositivo con il provisioning gratuito

Eseguire la procedura seguente per testare l'app Xamarin.iOS con il provisioning gratuito.

### <a name="use-xcode-to-create-a-signing-identity-and-provisioning-profile"></a>Usare Xcode per creare un'identità di firma e il profilo di provisioning

1. Se non si ha un ID Apple, [crearne uno](https://appleid.apple.com).
2. Aprire Xcode e passare a **Xcode > Preferences** (Preferenze).
3. In **Accounts** (Account) usare il pulsante **+** per aggiungere il proprio ID Apple esistente. La schermata dovrebbe essere simile alla seguente:

    ![Preferenze di Xcode: account](free-provisioning-images/launchapp1.png "Preferenze di Xcode: account")

4. Chiudere le preferenze di Xcode.
5. Collegare il dispositivo iOS in cui si vuole distribuire l'app.
6. Creare un nuovo progetto in Xcode. Scegliere **File > Nuovo > Progetto** e selezionare **App visualizzazione singola**.
7. Nella finestra di dialogo del nuovo progetto impostare **Team** all'ID Apple appena aggiunto. Nell'elenco a discesa dovrebbe essere simile a **Nome utente (Personal Team)** (Team personale):

    ![Creare una nuova app](free-provisioning-images/launchapp2.png "Creare una nuova app")

8. Dopo aver creato il nuovo progetto, scegliere uno schema di compilazione Xcode che abbia come destinazione il dispositivo iOS, anziché un simulatore.

    ![Selezionare uno schema di compilazione Xcode](free-provisioning-images/xcodescheme.png "Selezionare uno schema di compilazione Xcode")

9. Aprire le impostazioni di progetto dell'app selezionando il nodo di primo livello in **Project Navigator** (Selezione progetto) di Xcode.
10. In **Generale > Identità** assicurarsi che **Identificatore del bundle** _corrisponda esattamente_ all'identificatore del bundle dell'app Xamarin.iOS.

    ![Impostare un identificatore del bundle](free-provisioning-images/launchapp5.png "Impostare un identificatore del bundle")

    > [!IMPORTANT]
    > Xcode creerà un profilo di provisioning solo per un ID app esplicito che deve essere identico all'ID app dell'app Xamarin.iOS.
    > Se sono diversi, non sarà possibile usare il provisioning gratuito per distribuire l'app Xamarin.iOS.

11. In **Informazioni sulla distribuzione** assicurarsi che la destinazione di distribuzione corrisponda o sia inferiore alla versione di iOS installata nel dispositivo iOS connesso.
12. In **Firma in corso** selezionare **Automatically Manage Signing** (Gestisci automaticamente la firma) e selezionare il proprio team dall'elenco a discesa:

    ![Gestisci automaticamente la firma](free-provisioning-images/launchapp6.png "Gestisci automaticamente la firma")

    Xcode genera automaticamente un profilo di provisioning e un'identità di firma. È possibile vederlo facendo clic sull'icona delle informazioni accanto al profilo di provisioning:

    ![Visualizzare il profilo di provisioning](free-provisioning-images/launchapp7.png "Visualizzare il profilo di provisioning")

    > [!TIP]
    > Se si verifica un errore quando Xcode tenta di generare un profilo di provisioning, assicurarsi che lo schema di compilazione di Xcode attualmente selezionato abbia come destinazione il dispositivo iOS connesso anziché un simulatore.

13. Per eseguire il testing in Xcode, distribuire l'applicazione vuota nel dispositivo facendo clic sul pulsante Run (Esegui).

### <a name="deploy-your-xamarinios-app"></a>Distribuire l'app Xamarin.iOS

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

1. Connettere il dispositivo iOS all'host di compilazione Mac tramite USB o [in modalità wireless](~/ios/deploy-test/wireless-deployment.md).
2. Nel **riquadro della soluzione** di Visual Studio per Mac fare doppio clic su **Info.plist**.
3. In **Firma in corso** selezionare **Provisioning manuale**.
4. Fare clic su **Firma del bundle iOS** immagini (...).
5. Per **Configurazione**, selezionare **Debug**.
6. Per **Piattaforma**, selezionare **iPhone**.
7. Selezionare l'**Identità di firma** creata da Xcode.
8. Selezionare il **Profilo di provisioning** creato da Xcode.

    ![Impostare l'identità di firma e il profilo di provisioning](free-provisioning-images/launchapp8.png "Impostare l'identità di firma e il profilo di provisioning")

    > [!TIP]
    > Se l'identità di firma o il profilo di provisioning corretto non sono visualizzati, potrebbe essere necessario riavviare Visual Studio per Mac.

9. Fare clic su **OK** per salvare e chiudere **Opzioni progetto**.
10. Selezionare il dispositivo iOS ed eseguire l'app.

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. Assicurarsi che Visual Studio 2017 sia stato [associato a un host di compilazione Mac](~/ios/get-started/installation/windows/connecting-to-mac/index.md).
2. Connettere il dispositivo iOS all'host di compilazione Mac tramite USB o [in modalità wireless](~/ios/deploy-test/wireless-deployment.md).
3. In **Esplora soluzioni** di Visual Studio 2017 fare clic con il pulsante destro del mouse sul progetto Xamarin.iOS e scegliere **Proprietà**.
4. Passare a **Firma del bundle iOS**.
5. Per **Configurazione**, selezionare **Debug**.
6. Per **Piattaforma**, selezionare **iPhone**.
7. Selezionare **Provisioning manuale**.
8. Selezionare l'**Identità di firma** creata da Xcode.
9. Selezionare il **Profilo di provisioning** creato da Xcode.
    
    ![Impostare l'identità di firma e il profilo di provisioning](free-provisioning-images/setprofile-w157.png "Impostare l'identità di firma e il profilo di provisioning")

    > [!TIP]
    > Xcode ha creato questa identità di firma e questo profilo di provisioning e li ha archiviati nell'host di compilazione Mac. Sono accessibili a Visual Studio 2017 perché sono stati [abbinati](~/ios/get-started/installation/windows/connecting-to-mac/index.md) all'host di compilazione Mac. Se non sono elencati, potrebbe essere necessario riavviare Visual Studio 2017.

10. Salvare e chiudere le proprietà del progetto.
11. Selezionare il dispositivo iOS ed eseguire l'app.

-----

## <a name="limitations"></a>Limitazioni

Apple ha imposto alcune limitazioni relative a quando e come usare il provisioning gratuito per eseguire un'applicazione in un dispositivo iOS, in modo che sia consentito eseguire la distribuzione solo nel *proprio* dispositivo:

- L'accesso a iTunes Connect è limitato, pertanto servizi come la pubblicazione sull'App Store e TestFlight non sono disponibili per gli sviluppatori che eseguono il provisioning delle loro applicazioni gratuitamente. Un account Apple Developer (Enterprise o personale) è necessario per eseguire la distribuzione ad hoc e interna.
- I profili di provisioning creati con il provisioning gratuito scadono dopo una settimana, le identità di firma dopo un anno. 
- Dal momento che Xcode crea i profili di provisioning solo per ID app espliciti, è necessario seguire le [istruzioni riportate sopra](#testing-on-device-with-free-provisioning) per ogni app che si vuole installare.
- Il provisioning gratuito non consente il provisioning della maggior parte dei servizi delle applicazioni, tra cui Apple Pay, Game Center, iCloud, acquisti in-app, notifiche push e Wallet. Apple mette a disposizione un elenco completo di funzionalità nella guida [Supported capabilities (iOS)](https://help.apple.com/developer-account/#/dev21218dfd6) (Funzionalità di iOS supportate). Per eseguire il provisioning della propria app per l'uso con i servizi delle applicazioni, vedere la guida [Uso delle funzionalità](~/ios/deploy-test/provisioning/capabilities/index.md).

## <a name="summary"></a>Riepilogo

In questa guida sono stati illustrati i vantaggi e le limitazioni relativi all'uso del provisioning gratuito per installare applicazioni in un dispositivo iOS. È stata illustrata una procedura dettagliata su come usare il provisioning gratuito per installare un'app Xamarin.iOS.

## <a name="related-links"></a>Collegamenti correlati

- [Provisioning di dispositivi](~/ios/get-started/installation/device-provisioning/index.md)
- [Provisioning dei servizi delle applicazioni](~/ios/get-started/installation/device-provisioning/index.md#appservices)
