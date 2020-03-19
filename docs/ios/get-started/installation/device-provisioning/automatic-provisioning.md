---
title: Provisioning automatico per Xamarin.iOS
description: Dopo aver installato correttamente Xamarin.iOS, il passaggio successivo consiste nell'eseguire il provisioning del dispositivo iOS. Questa guida illustra l'uso della firma automatica per richiedere profili e certificati di sviluppo.
ms.prod: xamarin
ms.assetid: 81FCB2ED-687C-40BC-ABF1-FB4303034D01
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.custom: video
ms.date: 03/05/2020
ms.openlocfilehash: 069c40b74876bea1d3a0c8fca23b3d90c4b91635
ms.sourcegitcommit: 997f7b6a1a1bc50b98c3ca5bbc75d6875ba2ae9a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/18/2020
ms.locfileid: "79510679"
---
# <a name="automatic-provisioning-for-xamarinios"></a>Provisioning automatico per Xamarin.iOS

_Dopo aver installato correttamente Novell. iOS, il passaggio successivo per lo sviluppo di iOS consiste nel provisioning del dispositivo iOS. Questa guida esamina l'uso del provisioning automatico per richiedere certificati e profili di sviluppo._

## <a name="requirements"></a>Requisiti

Il provisioning automatico è disponibile in Visual Studio per Mac, Visual Studio 2019 e Visual Studio 2017 (versione 15,7 e successive). 

> [!NOTE]
> Per usare questa funzionalità, è necessario anche un account Apple Developer a pagamento. Altre informazioni sugli account per sviluppatori Apple sono disponibili nella Guida al [provisioning dei dispositivi](~/ios/get-started/installation/device-provisioning/index.md) .
> Se non si dispone di un account Apple Developer a pagamento, vedere la guida [provisioning gratuito per Novell. iOS] (~/iOS/Get-Started/Installation/Device-provisioning/Free-provisioning.MD).

> [!NOTE]
> Prima di iniziare, assicurarsi di accettare prima di tutto i contratti di licenza in [Apple Developer Portal](https://developer.apple.com/account/) o nell' [App Store Connect](https://appstoreconnect.apple.com/).


## <a name="enable-automatic-provisioning"></a>Abilitare il provisioning automatico

Prima di avviare il processo di firma automatica, è necessario assicurarsi di avere un ID Apple aggiunto in Visual Studio, come descritto nella Guida alla [gestione degli account Apple](~/cross-platform/macios/apple-account-management.md) . 

Dopo aver aggiunto un ID Apple, è possibile usare qualsiasi _team_ associato. In questo modo è possibile creare certificati, profili e altri ID per il team. L'ID del team viene usato anche per creare un prefisso per un ID app che verrà incluso nel profilo di provisioning. Questo ID consente a Apple di verificare l'identità dell'utente.

Per firmare automaticamente l'app per la distribuzione in un dispositivo iOS, seguire questa procedura:

# <a name="visual-studio-for-mac"></a>[Visual Studio per Mac](#tab/macos)

1. Aprire un progetto iOS in Visual Studio per Mac.

2. Aprire il file **Info.plist**.

3. Nella sezione **Firma** selezionare **Provisioning automatico**:

    ![Elenco a discesa per la selezione del team](automatic-provisioning-images/image2.png)

4. Selezionare il team nell'elenco a discesa **Team**.

5. Dopo alcuni secondi verranno creati un certificato di firma e un profilo di provisioning:

    ![Certificato e profilo creati correttamente](automatic-provisioning-images/image5.png)

    Se il processo di firma automatica non riesce, nel riquadro **Firma automatica** viene visualizzato il motivo dell'errore.

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

> [!NOTE]
> Se si usa Visual Studio 2017 o Visual Studio 2019 (versione 16,4 e precedenti), è necessario [associarlo a un host di compilazione Mac](~/ios/get-started/installation/windows/connecting-to-mac/index.md) prima di procedere.

1. Nella **Esplora soluzioni**fare clic con il pulsante destro del mouse sul nome del progetto iOS e scegliere **Proprietà**. Passare quindi alla scheda **firma bundle iOS** :

    ![Screenshot della pagina di firma del bundle nelle proprietà di iOS.](automatic-provisioning-images/bundle-signing-win.png)

2. Selezionare lo schema di **provisioning automatico** .

3. Selezionare il team dal menu a discesa **Team** per avviare il processo di firma automatica. Visual Studio indicherà se il processo è stato completato correttamente:

    ![Screenshot della pagina di firma del bundle che evidenzia il messaggio "provisioning automatico completato".](automatic-provisioning-images/signing-success-win.png)

-----

## <a name="run-automatic-provisioning"></a>Eseguire il provisioning automatico

Quando il provisioning automatico è abilitato, Visual Studio eseguirà di nuovo il processo, se necessario, quando si verifica una delle situazioni seguenti:

- Un dispositivo iOS viene collegato al Mac
  - Viene automaticamente verificato se il dispositivo è registrato sul portale Apple Developer. In caso contrario, verrà aggiunto e generato un nuovo profilo di provisioning che lo contiene.
- L'ID bundle dell'app viene modificato
  - Questa modifica causa l'aggiornamento dell'ID app. Viene quindi creato un nuovo profilo di provisioning che contiene questo ID app.
- Una funzionalità supportata viene abilitata nel file Entitlements.plist
  - Questa funzionalità viene aggiunta all'ID app e viene generato un nuovo profilo di provisioning con l'ID app aggiornato.
  - Attualmente non sono supportate tutte le funzionalità. Per altre informazioni su quelle supportate, vedere la guida [Uso delle funzionalità](~/ios/deploy-test/provisioning/capabilities/index.md).

## <a name="wildcard-app-ids"></a>ID app con caratteri jolly

In Visual Studio per Mac e Visual Studio 2019 (versione 16,5 o successiva), il provisioning automatico tenterà per impostazione predefinita di creare e usare un ID app con caratteri jolly e un profilo di provisioning invece di un ID app esplicito in base all' **identificatore del bundle** specificato in **info. plist**. Gli ID app con caratteri jolly riducono il numero di profili e ID da gestire nel portale per sviluppatori Apple.

In alcuni casi, l'entitlement dell'app richiede un ID app esplicito. Gli entitlement seguenti non supportano gli ID app con caratteri jolly:

- Gruppi di app
- Domini associati
- Apple Pay
- Area giochi
- HealthKit
- HomeKit
- Hotspot
- Acquisto in-app
- A percorsi multipli
- NFC
- VPN personale
- Notifiche push
- Configurazione degli accessori wireless

Se l'app usa uno di questi diritti, Visual Studio tenterà di creare un ID app esplicito (anziché un carattere jolly).

## <a name="troubleshoot"></a>Risoluzione dei problemi 

- L'approvazione di un nuovo account per sviluppatore Apple può richiedere diverse ore. Non sarà possibile abilitare il provisioning automatico fino a quando l'account non è stato approvato.
- Se il processo di provisioning automatico ha esito negativo con il messaggio di errore `Authentication Service Is Unavailable`, accedere a [App Store Connect](https://appstoreconnect.apple.com/) o [appleid.Apple.com](https://appleid.apple.com) per verificare di aver accettato i contratti di servizio più recenti.
- Se viene restituito il messaggio di errore `Authentication Error: Xcode 7.3 or later is required to continue developing with your Apple ID.`, assicurarsi che il team selezionato disponga di un abbonamento attivo al programma Apple Developer. Per usare un account Apple Developer a pagamento, vedere la Guida [relativa al provisioning gratuito per le app Novell. iOS](~/ios/get-started/installation/device-provisioning/free-provisioning.md) .

## <a name="related-links"></a>Collegamenti correlati

- [Provisioning gratuito](~/ios/get-started/installation/device-provisioning/free-provisioning.md)
- [Distribuzione di app](~/ios/deploy-test/app-distribution/index.md)
- [Risoluzione dei problemi](~/ios/deploy-test/troubleshooting.md)
- [Apple - App Distribution Guide](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/AppDistributionGuide/Introduction/Introduction.html) (Apple - Guida alla distribuzione dell'app)
