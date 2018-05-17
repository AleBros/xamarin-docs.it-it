---
title: Provisioning automatico
description: Dopo aver installato correttamente Xamarin.iOS, il passaggio successivo nello sviluppo iOS consiste nell'eseguire il provisioning del dispositivo iOS. Questa guida illustra l'uso della firma automatica per richiedere profili e certificati di sviluppo.
ms.prod: xamarin
ms.assetid: 81FCB2ED-687C-40BC-ABF1-FB4303034D01
ms.technology: xamarin-ios
author: asb3993
ms.author: amburns
ms.date: 05/06/2018
ms.openlocfilehash: 0e2ce758da2951efa0508e76cdf4eaac5384fa6b
ms.sourcegitcommit: e16517edcf471b53b4e347cd3fd82e485923d482
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/07/2018
---
# <a name="automatic-provisioning"></a>Provisioning automatico

_Dopo aver installato correttamente Xamarin.iOS, il passaggio successivo per lo sviluppo iOS consiste nell'eseguire il provisioning del dispositivo iOS. Questa guida illustra l'uso della firma automatica per richiedere profili e certificati di sviluppo._

## <a name="requirements"></a>Requisiti

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

- Visual Studio per Mac 7.3 o versione successiva
- Xcode 9 o versione successiva

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

- Visual Studio 2017 15.7 o versione successiva

È anche necessario disporre di un collegamento a un host di compilazione Mac che includa quanto segue:

- Xcode 9 o versione successiva

-----

## <a name="enabling-automatic-signing"></a>Abilitazione della firma automatica

Prima di avviare il processo di firma automatica, verificare di avere un ID Apple aggiunto in Visual Studio, come descritto nella guida [Apple Account Management](~/cross-platform/macios/apple-account-management.md) (Gestione degli account Apple). Dopo aver aggiunto un ID Apple, è possibile usare qualsiasi _team_ associato. In questo modo è possibile creare certificati, profili e altri ID per il team. L'ID del team viene usato anche per creare il prefisso di un ID app che verrà incluso nel profilo di provisioning. Questo ID consente a Apple di verificare l'identità dell'utente.

Per firmare automaticamente l'app per la distribuzione in un dispositivo iOS, seguire questa procedura:

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

1. Aprire un progetto iOS in Visual Studio per Mac.

2. Aprire il file **Info.plist**.

3. Nella sezione **Firma** selezionare **Provisioning automatico**:

    ![Elenco a discesa per la selezione del team](automatic-provisioning-images/image2.png)

4. Selezionare il team nell'elenco a discesa **Team**.

6. Dopo alcuni secondi verranno creati un certificato di firma e un profilo di provisioning:

    ![Certificato e profilo creati correttamente](automatic-provisioning-images/image5.png)

    Se il processo di firma automatica non riesce, nel riquadro **Firma automatica** viene visualizzato il motivo dell'errore.

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. Associare Visual Studio 2017 a un computer Mac, come descritto nella guida [Associazione al Mac](~/ios/get-started/installation/windows/connecting-to-mac/index.md).

2. Aprire le opzioni di provisioning, selezionando **Progetto > Provisioning Properties (Proprietà di provisioning)**

3. Selezionare lo schema **Automatically Provisioning** (Provisioning automatico):

    ![Selezione di uno schema automatico](automatic-provisioning-images/prov4.png)

4. Selezionare il team nella casella combinata **Team** per avviare il processo di firma automatica.

    ![Selezione del team](automatic-provisioning-images/prov3.png)

4. Viene avviato il processo di firma automatica. Visual Studio tenta di generare un ID dell'app, un profilo di provisioning e un'identità di firma da usare per la firma. È possibile visualizzare il processo di generazione nell'output di compilazione:

    ![Output di compilazione che mostra la generazione di elementi](automatic-provisioning-images/prov5.png)

-----

## <a name="triggering-automatic-provisioning"></a>Attivazione del provisioning automatico

Dopo l'abilitazione della firma automatica, Visual Studio per Mac aggiorna questi elementi, se necessario, quando si verifica uno degli eventi seguenti:

* Un dispositivo iOS viene collegato al Mac
    - Viene automaticamente verificato se il dispositivo è registrato sul portale Apple Developer. Se non lo è, viene aggiunto e viene generato un nuovo profilo di provisioning che lo contiene.
* L'ID bundle dell'app viene modificato
    - Questa modifica causa l'aggiornamento dell'ID app. Viene quindi creato un nuovo profilo di provisioning che contiene questo ID app.
* Una funzionalità supportata viene abilitata nel file Entitlements.plist
    - Questa funzionalità viene aggiunta all'ID app e viene generato un nuovo profilo di provisioning con l'ID app aggiornato.
    - Attualmente non sono supportate tutte le funzionalità. Per altre informazioni su quelle supportate, vedere la guida [Uso delle funzionalità](~/ios/deploy-test/provisioning/capabilities/index.md).


## <a name="related-links"></a>Collegamenti correlati

- [Provisioning gratuito](~/ios/get-started/installation/device-provisioning/free-provisioning.md)
- [Distribuzione di app](~/ios/deploy-test/app-distribution/index.md)
- [Risoluzione dei problemi](~/ios/deploy-test/troubleshooting.md)
- [Apple - App Distribution Guide](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/AppDistributionGuide/Introduction/Introduction.html) (Apple - Guida alla distribuzione dell'app)
