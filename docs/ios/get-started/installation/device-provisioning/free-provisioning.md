---
title: Provisioning gratuito
description: 'Con il rilascio di Xcode 7, Apple ha introdotto un importante cambiamento per tutti gli sviluppatori iOS e Mac: il provisioning gratuito.'
ms.prod: xamarin
ms.assetid: A5CE2ECF-8057-49ED-8393-EB0C5977FE4C
ms.technology: xamarin-ios
author: asb3993
ms.author: amburns
ms.date: 03/19/2017
ms.openlocfilehash: 09244ebaefedb991289e5a1f67b59491ee84ed28
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2018
---
# <a name="free-provisioning"></a>Provisioning gratuito

_Con il rilascio di Xcode 7, Apple ha introdotto un importante cambiamento per tutti gli sviluppatori iOS e Mac: il provisioning gratuito._

Il provisioning gratuito consente agli sviluppatori di distribuire la propria applicazione Xamarin.iOS nel proprio dispositivo iOS **senza** far parte di un **programma Apple Developer**. Questa possibilità è estremamente vantaggiosa per gli sviluppatori, in quanto il testing su un dispositivo è molto più efficace del testing sul simulatore, offrendo vantaggi in termini di memoria, archiviazione, connettività di rete e altro.

Il provisioning senza un account Apple Developer deve essere eseguito tramite Xcode, che crea un'*identità di firma* (contenente un certificato dello sviluppatore e una chiave privata) e un *profilo di provisioning* (contenente un ID app esplicito e l'UDID del dispositivo iOS connesso).

## <a name="requirements"></a>Requisiti

Per distribuire le proprie applicazioni Xamarin.iOS in un dispositivo mediante il provisioning gratuito, è necessario usare Xcode 7 o versione successiva.

**L'ID Apple usato allo scopo non deve essere connesso ad alcun programma Apple Developer.**

L'ID bundle usato nell'app deve essere univoco e non deve essere stato usato in un'altra app in precedenza. Qualsiasi ID bundle usato con il provisioning gratuito NON può essere riutilizzato. Se un'app è già stata distribuita, non è possibile eseguirne il provisioning con il provisioning gratuito. 

Per altre informazioni, vedere la guida [Distribuzione di app](~/ios/deploy-test/app-distribution/index.md).

Se la propria app usa i Servizi app, è necessario creare un profilo di provisioning, come descritto in dettaglio nella guida [Provisioning dei dispositivi](~/ios/get-started/installation/device-provisioning/index.md#appservices). Ulteriori limitazioni sono illustrate nella [sezione pertinente](#limitations) di seguito.


## <a name="a-namelaunching--launching-your-app"></a><a name="launching" /> Avvio dell'app

Per usare il provisioning gratuito per distribuire un'applicazione in un dispositivo, occorre usare Xcode per creare l'identità di firma e i profili di provisioning e quindi usare Visual Studio per Mac o Visual Studio per scegliere il profilo corretto con cui firmare l'app. A tale scopo, seguire questa procedura dettagliata:

1. Se non si ha un ID Apple, crearne uno su [appleid.apple.com](https://appleid.apple.com/account).
2. Aprire Xcode e passare a **Xcode > Preferences** (Preferenze).
3. In **Accounts** (Account) usare il pulsante **+** per aggiungere il proprio ID Apple esistente. La schermata dovrebbe essere simile alla seguente:

  [![](free-provisioning-images/launchapp1.png "Xcode - Preferences (Preferenze) - Accounts (Account)")](free-provisioning-images/launchapp1.png#lightbox)

4. Collegare il dispositivo iOS in cui si vuole distribuire l'applicazione e creare un nuovo progetto iOS a visualizzazione singola in Xcode. Impostare l'elenco a discesa **Team** sull'ID Apple appena aggiunto. Dovrebbe essere in un formato simile a `your name (Personal Team - your Apple ID)`:

  [![](free-provisioning-images/launchapp2.png "Creare l'identità di firma")](free-provisioning-images/launchapp2.png#lightbox)

5. Nella sezione **General > Identity** (Generale > Identità) verificare che l'identificatore del bundle corrisponda _esattamente_ all'identificatore del bundle della propria app Xamarin.iOS e che la destinazione di distribuzione corrisponda o sia inferiore al dispositivo iOS connesso. Questo passaggio è estremamente importante, in quanto Xcode creerà un profilo di provisioning solo con un ID app esplicito:

  [![](free-provisioning-images/launchapp5.png "Creare un profilo di provisioning con un ID app esplicito")](free-provisioning-images/launchapp5.png#lightbox)

6. Nella sezione Signing (Firma) selezionare **Automatically Manage Signing** (Gestisci automaticamente la firma) e selezionare il proprio team dall'elenco a discesa:

  [![](free-provisioning-images/launchapp6.png "Selezionare Automatically Manage Signing (Gestisci automaticamente la firma) e selezionare il proprio team dall'elenco a discesa")](free-provisioning-images/launchapp6.png#lightbox)

7. Il passaggio precedente genererà automaticamente un profilo di provisioning e un'identità di firma. È possibile vederlo facendo clic sull'icona delle informazioni accanto al profilo di provisioning:

  [![](free-provisioning-images/launchapp7.png "Visualizzare il profilo di provisioning")](free-provisioning-images/launchapp7.png#lightbox)

8. Per eseguire il testing in Xcode, distribuire l'applicazione vuota nel dispositivo facendo clic sul pulsante Run (Esegui).

9. Tornare all'IDE, con lo stesso dispositivo collegato, e fare clic con il pulsante destro del mouse sul progetto Xamarin.iOS per aprire la finestra di dialogo **Opzioni progetto**. Passare alla sezione Firma del bundle iOS e impostare in modo esplicito l'identità di firma e il profilo di provisioning:

  [![](free-provisioning-images/launchapp8.png "Impostare l'identità di firma e il profilo di provisioning")](free-provisioning-images/launchapp8.png#lightbox)

Se l'identità di firma o il profilo di provisioning corretto non è visualizzato nell'IDE, potrebbe essere necessario riavviarlo.


## <a name="a-namelimitations-limitations"></a><a name="limitations" />Limitazioni

Apple ha imposto alcune limitazioni relative a quando e come usare il provisioning gratuito per eseguire la propria applicazione in un dispositivo iOS, in modo che sia consentito eseguire la distribuzione solo nel *proprio* dispositivo. Queste limitazioni sono elencate in questa sezione.

Anche l'accesso a iTunes Connect è limitato, pertanto servizi come la pubblicazione sull'App Store e TestFlight non sono disponibili per gli sviluppatori che eseguono il provisioning delle loro applicazioni gratuitamente. Un account Apple Developer (Enterprise o personale) è necessario per eseguire la distribuzione ad hoc e interna.

I profili di provisioning creati in questo modo scadono dopo una settimana, le identità di firma dopo un anno. Inoltre, i profili di provisioning vengono creati solo con ID app espliciti, quindi è necessario seguire le istruzioni riportate [sopra](#launching) per ogni app che si vuole installare.

Infine, il provisioning gratuito non consente il provisioning della maggior parte dei servizi di applicazioni, vale a dire:

- Apple Pay
- Game Center
- iCloud
- Acquisti in-app
- Notifiche push
- Wallet (in precedenza Passbook)

Un elenco completo è messo a disposizione da Apple nella guida [Supported Capabilities](https://developer.apple.com/library/prerelease/ios/documentation/IDEs/Conceptual/AppDistributionGuide/SupportedCapabilities/SupportedCapabilities.html#//apple_ref/doc/uid/TP40012582-CH38-SW1) (Funzionalità supportate). Per eseguire il provisiong della propria app per l'uso con i servizi di applicazioni, vedere la guida [Uso delle funzionalità](~/ios/deploy-test/provisioning/capabilities/index.md).


## <a name="summary"></a>Riepilogo

In questa guida sono stati illustrati i vantaggi e le limitazioni relativi all'uso del provisioning gratuito per installare applicazioni in un dispositivo iOS. Sono inoltre state descritte le procedure dettagliate relative all'uso del provisioning gratuito per installare un'app Xamarin.iOS.

## <a name="related-links"></a>Collegamenti correlati

- [Provisioning dei dispositivi](~/ios/get-started/installation/device-provisioning/index.md)
- [Provisioning dei servizi per le applicazioni](~/ios/get-started/installation/device-provisioning/index.md#appservices)
