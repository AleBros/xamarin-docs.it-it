---
title: Distribuzione e test delle App watchOS con Xamarin
description: Questo documento descrive come distribuire e testare l'App watchOS compilate con Xamarin. Fornisce un elenco di controllo di distribuzione, si esamina esplicito e gli ID, di app con caratteri jolly e si esaminano i gruppi di app.
ms.prod: xamarin
ms.assetid: 98257399-E9B3-4BAB-9204-0E89117DEA6D
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/17/2017
ms.openlocfilehash: a0738b03c4fa0ad975b872307bb17f387b1c5fd5
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2018
ms.locfileid: "50120957"
---
# <a name="deploying-and-testing-watchos-apps-with-xamarin"></a>Distribuzione e test delle App watchOS con Xamarin

## <a name="deployment-checklist"></a>Elenco di controllo distribuzione

Se si distribuisce a un test di espressione di controllo, o caricare l'App Store, è necessario completare i passaggi in questa pagina:

- Nel **iOS Dev Center**:
  - [ID dell'app](#App_IDs) sono stati creati.
  - [I gruppi di app](#App_Groups) configurare (se richiesto).
  - Profili di Provisioning di distribuzione creata

- Nella soluzione:

  - Verificare i [riferimenti a progetti e gli ID Bundle](~/ios/watchos/get-started/installation.md) sono impostate.
  - Controllare le icone siano [correttamente configurato](~/ios/watchos/app-fundamentals/icons.md).
  - Verificare la corrispondenza di numeri di versione bundle in tutti i progetti.
  - Configurare il **entitlements. plist** per App i gruppi (se richiesto).

* Seguire quindi le istruzioni per:
  - [Distribuire un Apple Watch per i test](~/ios/watchos/deploy-test/device.md), o
  - [Caricare l'App Store](~/ios/watchos/deploy-test/appstore.md).

<a name="App_IDs"/>

## <a name="app-ids"></a>ID dell'App

Come descritto nel [istruzioni di installazione](~/ios/watchos/get-started/installation.md), tutte e tre i progetti in un'App sono correlati gli ID Bundle, ad esempio:

- Progetto unificata di xamarin. ios: `com.xamarin.WatchKitCatalog`
- Progetto estensione WatchKit: `com.xamarin.WatchKitCatalog.watchkitextension`
- Progetto di App Watch- `com.xamarin.WatchKitCatalog.watchkitapp`

Tutti e tre i progetti richiedono un corrispondente distribuzione profilo di Provisioning, usando in modo esplicito gli ID delle App per ogni o un carattere jolly ID dell'App.

### <a name="explicit-app-ids"></a>ID App esplicito

Creare un **App ID** per l'ID Bundle ogni di ogni progetto (che avrà un aspetto simile al seguente nel centro per sviluppatori iOS):

![Gli ID Bundle nel centro per sviluppatori iOS](images/appids-specific-sml.png)

Durante la creazione o la configurazione di ID App, ricordare di abilitare le funzionalità specifiche richieste dall'applicazione. Ad esempio le notifiche push e i gruppi di app.

È necessario creare un profilo di Provisioning di distribuzione per ogni ID. App

### <a name="wildcard-app-id"></a>ID dell'App con caratteri jolly

In alternativa, è possibile creare un carattere jolly **App ID** che corrisponde a tutti e tre i progetti, ad esempio `com.xamarin.*`.

Si noti che alcune funzionalità non possono essere utilizzate con un ID App con carattere jolly (ad esempio le notifiche push). Se l'app richiede queste funzionalità è necessario creare ID App espliciti.

Per la distribuzione, è solo necessario creare una distribuzione profilo di Provisioning per il carattere jolly ID dell'App.

<a name="App_Groups" />

## <a name="app-groups"></a>Gruppi di app

È possibile usare un gruppo di App di condividere dati tra le App di iOS e l'estensione di espressioni di controllo. È necessario assicurarsi che la soluzione contiene:

- Configurato il **gruppo di App** del portale per sviluppatori Apple **i certificati, identificatori e profili** sezione.

- Abilitata **gruppi di App** (e fornito il **ID del gruppo di App**) in *entrambi* App iOS e l'estensione di espressioni di controllo **ID App** e  **Entitlements. plist**.

### <a name="certificates-identifiers--profiles"></a>I certificati, identificatori e profili

Per usare un gruppo di App, creare una voce nella **gruppi di App** dello schermo. Nell'esempio seguente il gruppo è denominato con lo stesso stile di DNS inverso comunemente usato per individuare gli ID App, ma con la `group.` il prefisso (obbligatorio):

![L'identificatore](images/appgroups-new-sml.png)

Il gruppo di app verrà quindi visualizzato nell'elenco:

![L'elenco di identificatore](images/appgroups-setup-sml.png)

Dopo aver creato il gruppo, è possibile farvi riferimento nel **App ID** configurazione. Ricordarsi di includerlo iOS entrambe le App e l'estensione Watch **App ID**.

![Consifurations disponibili](images/appgroups-sml.png)

Effettuare **non** Abilita gruppi di App nell'ID dell'App Apple Watch. Non è necessaria l'attivazione nella stesso Apple watch.

### <a name="entitlementsplist"></a>Entitlements.plist

Alcune funzionalità delle app (ad es. I gruppi di App) è necessario impostare i diritti.
Fare doppio clic per modificare la **entitlements. plist** file in questi progetti:

- progetto di App iOS
- Progetto di estensione delle espressioni di controllo

.![L'editor entitlements. plist](images/entitlements-plist-sml.png)

Effettuare **non** Abilita gli Entitlement nel progetto di App Watch. Non è necessaria l'attivazione nella stesso Apple watch.

## <a name="related-links"></a>Collegamenti correlati

- [Guida di Apple WatchKit invio](https://developer.apple.com/app-store/watch/)
