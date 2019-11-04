---
title: Provisioning automatico per Xamarin.iOS
description: Dopo aver installato correttamente Xamarin.iOS, il passaggio successivo consiste nell'eseguire il provisioning del dispositivo iOS. Questa guida illustra l'uso della firma automatica per richiedere profili e certificati di sviluppo.
ms.prod: xamarin
ms.assetid: 81FCB2ED-687C-40BC-ABF1-FB4303034D01
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.custom: video
ms.date: 01/22/2019
ms.openlocfilehash: bdc8366e75455755cbb2f533b6707f72e33436e2
ms.sourcegitcommit: 3ea19e3a51515b30349d03c70a5b3acd7eca7fe7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/01/2019
ms.locfileid: "73425579"
---
# <a name="automatic-provisioning-for-xamarinios"></a>Provisioning automatico per Xamarin.iOS

_Dopo aver installato correttamente Novell. iOS, il passaggio successivo per lo sviluppo di iOS consiste nel provisioning del dispositivo iOS. Questa guida esamina l'uso della firma automatica per richiedere certificati e profili di sviluppo._

## <a name="requirements"></a>Requisiti

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

- Visual Studio per Mac 7.3 o versione successiva
- Xcode 9 o versione successiva

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

- Visual Studio 2019
- oppure Visual Studio 2017 15.7 o versione successiva

È anche necessario disporre di un collegamento a un host di compilazione Mac che includa quanto segue:

- Xcode 10 o versione successiva

-----

## <a name="enabling-automatic-signing"></a>Abilitazione della firma automatica

Prima di avviare il processo di firma automatica, verificare di avere un ID Apple aggiunto in Visual Studio, come descritto nella guida [Apple Account Management](~/cross-platform/macios/apple-account-management.md) (Gestione degli account Apple). Dopo aver aggiunto un ID Apple, è possibile usare qualsiasi _team_ associato. In questo modo è possibile creare certificati, profili e altri ID per il team. L'ID del team viene usato anche per creare un prefisso per un ID app che verrà incluso nel profilo di provisioning. Questo ID consente a Apple di verificare l'identità dell'utente.

> [!IMPORTANT]
> Prima di iniziare, assicurarsi di accedere a [iTunes Connect](https://itunesconnect.apple.com/) oppure [appleid.apple.com](https://appleid.apple.com) per verificare di avere accettato i criteri per l'account Apple più recenti. Se richiesto, completare i passaggi per accettare eventuali nuovi contratti per l'account da Apple. Se non si accetta l'informativa sulla privacy di maggio 2018, verrà visualizzato uno degli avvisi seguenti quando si tenta di eseguire il provisioning del dispositivo:
>
> ```
> Unexpected authentication failure. Reason: {
> "authType" : "sa"
> }
> ```
>
> Oppure
>
> ```
> Authentication Service Is Unavailable
> ```

Per firmare automaticamente l'app per la distribuzione in un dispositivo iOS, seguire questa procedura:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

1. Aprire un progetto iOS in Visual Studio per Mac.

2. Aprire il file **Info.plist**.

3. Nella sezione **Firma** selezionare **Provisioning automatico**:

    ![Elenco a discesa per la selezione del team](automatic-provisioning-images/image2.png)

4. Selezionare il team nell'elenco a discesa **Team**.

5. Dopo alcuni secondi verranno creati un certificato di firma e un profilo di provisioning:

    ![Certificato e profilo creati correttamente](automatic-provisioning-images/image5.png)

    Se il processo di firma automatica non riesce, nel riquadro **Firma automatica** viene visualizzato il motivo dell'errore.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. Associare Visual Studio 2019 a un computer Mac, come descritto nella guida [Associazione al Mac](~/ios/get-started/installation/windows/connecting-to-mac/index.md).

2. In **Esplora soluzioni** fare clic con il pulsante destro del mouse sul nome del progetto e selezionare **Proprietà**. Passare quindi alla scheda **Firma del bundle iOS**.

3. Selezionare lo schema **Provisioning automatico**:

    ![Selezione di uno schema automatico](automatic-provisioning-images/prov4.png)

4. Selezionare il team nella casella combinata **Team** per avviare il processo di firma automatica.

    ![Selezione del team](automatic-provisioning-images/prov3.png)

5. Viene avviato il processo di firma automatica. Visual Studio tenta di generare un ID dell'app, un profilo di provisioning e un'identità di firma da usare per la firma. È possibile visualizzare il processo di generazione nell'output di compilazione:

    ![Output di compilazione che mostra la generazione di elementi](automatic-provisioning-images/prov5.png)

-----

## <a name="triggering-automatic-provisioning"></a>Attivazione del provisioning automatico

Dopo l'abilitazione della firma automatica, Visual Studio per Mac aggiorna questi elementi, se necessario, quando si verifica uno degli eventi seguenti:

- Un dispositivo iOS viene collegato al Mac
  - Viene automaticamente verificato se il dispositivo è registrato sul portale Apple Developer. Se non lo è, viene aggiunto e viene generato un nuovo profilo di provisioning che lo contiene.
- L'ID bundle dell'app viene modificato
  - Questa modifica causa l'aggiornamento dell'ID app. Viene quindi creato un nuovo profilo di provisioning che contiene questo ID app.
- Una funzionalità supportata viene abilitata nel file Entitlements.plist
  - Questa funzionalità viene aggiunta all'ID app e viene generato un nuovo profilo di provisioning con l'ID app aggiornato.
  - Attualmente non sono supportate tutte le funzionalità. Per altre informazioni su quelle supportate, vedere la guida [Uso delle funzionalità](~/ios/deploy-test/provisioning/capabilities/index.md).

## <a name="wildcard-app-ids"></a>ID app con caratteri jolly

A partire da Visual Studio per Mac 7.6, il provisioning automatico tenta di creare e usare l'ID app con caratteri jolly i profili di provisioning per impostazione predefinita, anziché un ID app esplicito in base all'**identificatore del bundle** specificato in **Info.plist**. Gli ID app con caratteri jolly riducono il numero di profili e ID da gestire nel portale per sviluppatori Apple.

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

Se l'app usa uno di questi entitlement, Visual Studio per Mac proverà a creare un ID app esplicito (anziché un carattere jolly).

> [!NOTE]
> Il provisioning automatico con ID app con caratteri jolly è attualmente disponibile solo in Visual Studio per Mac.

## <a name="related-links"></a>Collegamenti correlati

- [Provisioning gratuito](~/ios/get-started/installation/device-provisioning/free-provisioning.md)
- [Distribuzione di app](~/ios/deploy-test/app-distribution/index.md)
- [Risoluzione dei problemi](~/ios/deploy-test/troubleshooting.md)
- [Apple - App Distribution Guide](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/AppDistributionGuide/Introduction/Introduction.html) (Apple - Guida alla distribuzione dell'app)

## <a name="related-video"></a>Video correlato

> [!Video https://channel9.msdn.com/Shows/XamarinShow/Snack-Pack-Simplified-iOS-Provisioning-in-Visual-Studio-with-fastlane/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]
