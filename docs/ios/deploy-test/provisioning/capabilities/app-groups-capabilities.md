---
title: Funzionalità Gruppo di app in Xamarin.iOS
description: L'aggiunta di funzionalità a un'applicazione spesso richiede un'ulteriore configurazione del provisioning. Questa guida illustra la configurazione necessaria per le funzionalità Gruppo di app.
ms.prod: xamarin
ms.assetid: 0A61220B-BBAC-492B-9D3B-578986E64064
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/15/2017
ms.openlocfilehash: 20aa1da478916bf4c8949103a5ce7fbf1f5d8f93
ms.sourcegitcommit: 3d21bb1a6d9b78b65aa49917b545c39d44aa3e3c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70064831"
---
# <a name="app-group-capabilities-in-xamarinios"></a>Funzionalità Gruppo di app in Xamarin.iOS

_L'aggiunta di funzionalità a un'applicazione spesso richiede un'ulteriore configurazione del provisioning. Questa guida illustra la configurazione necessaria per le funzionalità Gruppo di app._

Un Gruppo di app consente a diverse applicazioni o a un'applicazione e alle relative estensioni di accedere a un percorso di archiviazione file condiviso. È possibile usare i Gruppi di app per dati quali:

* [Impostazioni di Apple Watch](~/ios/watchos/app-fundamentals/settings.md)
* [NSUserDefaults condivisa](~/ios/app-fundamentals/user-defaults.md)
* [File condivisi](~/ios/watchos/app-fundamentals/parent-app.md#files)

## <a name="configure-a-new-app-group"></a>Configurare un nuovo Gruppo di app

Il percorso condiviso viene configurato usando un [Gruppo di app](https://developer.apple.com/library/content/documentation/Miscellaneous/Reference/EntitlementKeyReference/Chapters/EnablingAppSandbox.html#//apple_ref/doc/uid/TP40011195-CH4-SW19) configurato nella sezione **Certificates, Identifiers & Profiles** (Certificati, identificatori e profili) del [centro sviluppatori Apple](https://developer.apple.com/account/). È necessario fare riferimento a questo valore anche nel file Entitlements.plist di ogni progetto.

Il gruppo di app avrà un identificatore che in genere corrisponde all'ID bundle con il . Per l'ID bundle `com.xamarin.WatchSettings` , ad esempio, il gruppo di app sarà `group.com.xamarin.WatchSettings`.

Per creare un nuovo Gruppo di app, eseguire le operazioni seguenti:

1. Visitare  [iOS Developer Center](https://developer.apple.com/account/) di Apple, aprire il proprio **account** e accedere.
2. Selezionare **Certificates, IDs & Profiles** (Certificati, ID e profili).
3. In **Identifiers** (Identificatori) selezionare **App Groups** (Gruppi di app) e fare clic sul pulsante **+** per creare un nuovo gruppo.
4. Immettere un **nome** e un **identificatore** per il nuovo gruppo e fare clic sul pulsante **Continue** (Continua): 
   
    ![Aggiungere i dettagli del Gruppo di app](app-groups-capabilities-images/image52.png)

5. Fare clic sul pulsante **Register** (Registra) per creare il gruppo e quindi su **Done** (Chiudi) per tornare all'elenco dei Gruppi di app registrati.

## <a name="configure-an-app-to-use-app-groups"></a>Configurare un'app per l'uso dei Gruppi di app

Dopo aver creato il Gruppo di app, configurare gli ID delle app in modo che le app possano usarlo.

Eseguire le operazioni seguenti:

1. Visitare  [iOS Developer Center](https://developer.apple.com/account/) di Apple e accedere con un account sviluppatore Apple.
2. Nel menu **Program Resources** (Risorse del programma) selezionare **Certificates, IDs & Profiles** (Certificati, ID e profili).
3. In **Identifiers** (Identificatori) selezionare **App IDs** (ID app) e fare clic sul pulsante **+** per creare un nuovo ID.
4. Immettere un nome per l'ID app e assegnargli un ID app esplicito.
5. In **App Services** (Servizi app) abilitare **App Groups** (Gruppi di app) e quindi fare clic sul pulsante Continue (Continua):

    ![Aggiungere i servizi app del Gruppo di app](app-groups-capabilities-images/image53.png)

6. Verificare le impostazioni e fare clic sul pulsante **Register** (Registra) per creare l'ID app.
7. Fare clic sul pulsante **Done** (Chiudi) per tornare all'elenco degli ID app registrati.
8. Selezionare dall'elenco l'ID app appena creato e fare clic sul pulsante **Edit** (Modifica):

    ![Selezionare l'ID app dall'elenco](app-groups-capabilities-images/image54.png)

9. In Service (Servizio) **App Group** (Gruppo di app) fare clic sul pulsante **Edit** (Modifica):

    ![Selezionare l'ID app dall'elenco](app-groups-capabilities-images/image55.png)

10. Selezionare il Gruppo di app creato in precedenza e fare clic sul pulsante **Continue** (Continua):

    ![Aggiungere il Gruppo di app](app-groups-capabilities-images/image56.png)

11. Fare clic sul pulsante **Assign** (Assegna) e quindi sul pulsante **Done** (Chiudi) per tornare all'elenco degli ID app registrati.
12. Ripetere questi passaggi per tutte le app o le estensioni che useranno il Gruppo di app.

## <a name="next-steps"></a>Passaggi successivi
 
Nell'elenco seguente vengono descritti i passaggi aggiuntivi che potrebbero essere necessari:

* Usare lo spazio dei nomi del framework nell'app.
* Aggiungere all'app gli entitlement necessari. Per informazioni dettagliate sugli entitlement necessari e su come aggiungerli, vedere la guida [Uso degli entitlement](~/ios/deploy-test/provisioning/entitlements.md).
* In **Firma del bundle iOS** dell'app assicurarsi che **Entitlement personalizzati** sia impostato su **Entitlements.plist**. Questa _non_ è l'impostazione predefinita per le build di debug e del simulatore iOS.

In caso di problemi con i servizi app, vedere la sezione [Troubleshooting](~/ios/deploy-test/provisioning/capabilities/index.md) (Risoluzione dei problemi) della guida principale.
