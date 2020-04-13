---
title: Provisioning automatico per Xamarin.iOS
description: Dopo aver installato correttamente Xamarin.iOS, il passaggio successivo nello sviluppo iOS consiste nell'eseguire il provisioning del dispositivo iOS. Questa guida illustra l'uso della firma automatica per richiedere profili e certificati di sviluppo.
ms.prod: xamarin
ms.assetid: 81FCB2ED-687C-40BC-ABF1-FB4303034D01
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.custom: video
ms.date: 03/05/2020
ms.openlocfilehash: 069c40b74876bea1d3a0c8fca23b3d90c4b91635
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2020
ms.locfileid: "79510679"
---
# <a name="automatic-provisioning-for-xamarinios"></a>Provisioning automatico per Xamarin.iOS

_Una volta che Xamarin.iOS è stato installato con successo, il passo successivo nello sviluppo di iOS è quello di eseguire il provisioning del dispositivo iOS. Questa guida illustra l'uso del provisioning automatico per richiedere certificati e profili di sviluppo._

## <a name="requirements"></a>Requisiti

Il provisioning automatico è disponibile in Visual Studio per Mac, Visual Studio 2019 e Visual Studio 2017 (versione 15.7 e successive). 

> [!NOTE]
> Devi anche avere un account Apple Developer a pagamento per utilizzare questa funzione. Ulteriori informazioni sugli account per sviluppatori Apple sono disponibili nella guida [Provisioning dei dispositivi.](~/ios/get-started/installation/device-provisioning/index.md)
> Se non disponi di un account sviluppatore Apple a pagamento, consulta la guida [Provisioning gratuito per Xamarin.iOS] (sezione : : / ios /get-started/installazione/device-provisioning/free-provisioning.md).

> [!NOTE]
> Prima di iniziare, assicurati di accettare eventuali contratti di licenza nel [portale per sviluppatori Apple](https://developer.apple.com/account/) o [nell'App Store Connect.](https://appstoreconnect.apple.com/)


## <a name="enable-automatic-provisioning"></a>Abilitare il provisioning automatico

Prima di avviare il processo di firma automatica, devi assicurarti di avere aggiunto un ID Apple in Visual Studio, come descritto nella Guida alla [gestione degli account Apple.](~/cross-platform/macios/apple-account-management.md) 

Dopo aver aggiunto un ID Apple, è possibile usare qualsiasi _team_ associato. In questo modo è possibile creare certificati, profili e altri ID per il team. L'ID team viene utilizzato anche per creare un prefisso per un ID app che verrà incluso nel profilo di provisioning. Questo ID consente a Apple di verificare l'identità dell'utente.

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
> Se si usa Visual Studio 2017 o Visual Studio 2019 (versione 16.4 e versioni precedenti), è necessario essere associati a un host di [compilazione Mac](~/ios/get-started/installation/windows/connecting-to-mac/index.md) prima di procedere.

1. In **Esplora soluzioni**fare clic con il pulsante destro del mouse sul nome del progetto iOS e scegliere **Proprietà**. Quindi, passare alla scheda Firma bundle iOS:Then, navigate to the **iOS Bundle Signing** tab:

    ![Screenshot della pagina di firma del bundle nelle proprietà di iOS.](automatic-provisioning-images/bundle-signing-win.png)

2. Selezionare lo schema **di provisioning automatico.**

3. Seleziona il tuo team dal menu a discesa **Team** per avviare il processo di firma automatica. Visual Studio indicherà se il processo è stato completato correttamente:Visual Studio will indicate if the process successfully:

    ![Screenshot della pagina di firma del bundle che evidenzia il messaggio "Provisioning automatico completato correttamente".](automatic-provisioning-images/signing-success-win.png)

-----

## <a name="run-automatic-provisioning"></a>Eseguire il provisioning automatico

Quando il provisioning automatico è abilitato, Visual Studio eseguirà nuovamente il processo se necessario quando si verifica una delle operazioni seguenti:When automatic provisioning is enabled, Visual Studio will re-run the process if necessary when any of the following things happen:

- Un dispositivo iOS viene collegato al Mac
  - Viene automaticamente verificato se il dispositivo è registrato sul portale Apple Developer. In caso contrario, lo aggiungerà e genererà un nuovo profilo di provisioning che lo contiene.
- L'ID bundle dell'app viene modificato
  - Questa modifica causa l'aggiornamento dell'ID app. Viene quindi creato un nuovo profilo di provisioning che contiene questo ID app.
- Una funzionalità supportata viene abilitata nel file Entitlements.plist
  - Questa funzionalità viene aggiunta all'ID app e viene generato un nuovo profilo di provisioning con l'ID app aggiornato.
  - Attualmente non sono supportate tutte le funzionalità. Per altre informazioni su quelle supportate, vedere la guida [Uso delle funzionalità](~/ios/deploy-test/provisioning/capabilities/index.md).

## <a name="wildcard-app-ids"></a>ID app con caratteri jolly

In Visual Studio per Mac e Visual Studio 2019 (versione 16.5 o successiva), il provisioning automatico tenterà per impostazione predefinita di creare e utilizzare un ID app e un profilo di provisioning con caratteri jolly anziché un ID app esplicito basato **sull'identificatore del bundle** specificato in **Info.plist**. Gli ID app con caratteri jolly riducono il numero di profili e ID da gestire nel portale per sviluppatori Apple.

In alcuni casi, l'entitlement dell'app richiede un ID app esplicito. Gli entitlement seguenti non supportano gli ID app con caratteri jolly:

- Gruppi di app
- Domini associati
- Apple Pay
- Game Center
- HealthKit
- HomeKit
- Hotspot
- Acquisto in-app
- A percorsi multipli
- NFC
- VPN personale
- Notifiche push
- Configurazione accessori wireless

Se l'app usa uno di questi permessi, Visual Studio tenterà di creare un ID app esplicito (anziché un carattere jolly).

## <a name="troubleshoot"></a>Risolvere problemi 

- L'approvazione di un nuovo account sviluppatore Apple potrebbe richiedere diverse ore. Non sarà possibile abilitare il provisioning automatico fino a quando l'account non sarà stato approvato.
- Se il processo di provisioning `Authentication Service Is Unavailable`automatico non riesce con il messaggio di errore , accedere ad [App Store Connect](https://appstoreconnect.apple.com/) o [appleid.apple.com](https://appleid.apple.com) per verificare di aver accettato i contratti di servizio più recenti.
- Se ricevi il `Authentication Error: Xcode 7.3 or later is required to continue developing with your Apple ID.`messaggio di errore, assicurati che il team selezionato abbia un'iscrizione a pagamento attiva all'Apple Developer Program. Per utilizzare un account sviluppatore Apple a pagamento, consulta la guida [Provisioning gratuito per le app Xamarin.iOS.](~/ios/get-started/installation/device-provisioning/free-provisioning.md)

## <a name="related-links"></a>Collegamenti correlati

- [Provisioning gratuito](~/ios/get-started/installation/device-provisioning/free-provisioning.md)
- [Distribuzione app](~/ios/deploy-test/app-distribution/index.md)
- [Risoluzione dei problemi](~/ios/deploy-test/troubleshooting.md)
- [Apple - App Distribution Guide](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/AppDistributionGuide/Introduction/Introduction.html) (Apple - Guida alla distribuzione dell'app)
