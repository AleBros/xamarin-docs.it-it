---
title: Provisioning automatico
description: Dopo aver installato correttamente Xamarin.iOS, il passaggio successivo nello sviluppo iOS consiste nell'eseguire il provisioning del dispositivo iOS. Questa guida illustra l'uso della firma automatica in Visual Studio per Mac per richiedere profili e certificati di sviluppo.
ms.topic: article
ms.prod: xamarin
ms.assetid: 81FCB2ED-687C-40BC-ABF1-FB4303034D01
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 11/17/2017
ms.openlocfilehash: d7532d052c57ad46caca0cd6d6ce26d0e77dc05f
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2018
---
# <a name="automatic-provisioning"></a>Provisioning automatico

_Dopo aver installato correttamente Xamarin.iOS, il passaggio successivo per lo sviluppo iOS consiste nell'eseguire il provisioning del dispositivo iOS. Questa guida illustra l'uso della firma automatica in Visual Studio per Mac per richiedere profili e certificati di sviluppo._

## <a name="requirements"></a>Requisiti

- Visual Studio per Mac 7.3 o versione successiva
- Xcode 9 o versione successiva

> [!IMPORTANT]
>  Questa guida illustra come usare Visual Studio per Mac per configurare un dispositivo Apple per la distribuzione e come distribuire un'applicazione. Per le procedure manuali o per le istruzioni valide per Visual Studio su Windows, è consigliabile seguire i passaggi dettagliati nella guida al [provisioning manuale](~/ios/get-started/installation/device-provisioning/manual-provisioning.md).

## <a name="enabling-automatic-signing"></a>Abilitazione della firma automatica

Prima di avviare il processo di firma automatica, verificare di avere un ID Apple aggiunto in Visual Studio per Mac, come descritto nella guida [Apple Account Management](~/cross-platform/macios/apple-account-management.md) (Gestione degli account Apple). Dopo aver aggiunto un ID Apple, è possibile usare qualsiasi _team_ associato. In questo modo è possibile creare certificati, profili e altri ID per il team. L'ID del team viene usato anche per creare il prefisso di un ID app che verrà incluso nel profilo di provisioning. Questo ID consente a Apple di verificare l'identità dell'utente.

Per firmare automaticamente l'app per la distribuzione in un dispositivo iOS, seguire questa procedura:

1. Aprire un progetto iOS in Visual Studio per Mac.

2. Aprire il file **Info.plist**:

    ![File Info.plist](automatic-provisioning-images/image1.png)

3. Nella sezione **Firma** selezionare il proprio team nell'elenco a discesa **Team**:

    ![Elenco a discesa per la selezione del team](automatic-provisioning-images/image2.png)

4. Selezionare la casella di controllo **Automatically manage signing** (Gestisci automaticamente la firma), come illustrato nella figura seguente. Viene avviato il processo di firma automatica, in cui Visual Studio per Mac tenta di generare un ID app, un profilo di provisioning e un'identità di firma e di impostare l'uso di questi elementi per la firma. La selezione della casella di controllo disabilita i controlli manuali per la selezione delle identità di firma.

    ![Opzione Automatically manage signing (Gestisci automaticamente la firma)](automatic-provisioning-images/image3.png)

5. Si apre la finestra di dialogo seguente, che informa che il file di progetto verrà modificato in modo da usare il certificato e il profilo di provisioning appena generati:

    ![Finestra di dialogo che informa che il file di progetto verrà modificato](automatic-provisioning-images/image4.png)

6. Dopo alcuni secondi vengono creati e visualizzati un certificato di firma e un profilo di provisioning:

    ![Certificato e profilo creati correttamente](automatic-provisioning-images/image5.png)

    Se il processo di firma automatica non riesce, nel riquadro **Firma automatica** viene visualizzato il motivo dell'errore.

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
