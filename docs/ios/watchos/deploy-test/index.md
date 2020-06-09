---
title: Distribuzione e test di app watchos con Novell
description: Questo documento descrive come distribuire e testare le app watchos compilate con Novell. Viene fornito un elenco di controllo per la distribuzione, vengono illustrati gli ID app espliciti e con caratteri jolly e vengono esaminati i gruppi di app.
ms.prod: xamarin
ms.assetid: 98257399-E9B3-4BAB-9204-0E89117DEA6D
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/17/2017
ms.openlocfilehash: 4e2ff46174d9dbb9171a470c389ffe301f6d0d60
ms.sourcegitcommit: 93e6358aac2ade44e8b800f066405b8bc8df2510
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/09/2020
ms.locfileid: "84569647"
---
# <a name="deploying-and-testing-watchos-apps-with-xamarin"></a>Distribuzione e test di app watchos con Novell

## <a name="deployment-checklist"></a>Elenco di controllo distribuzione

Se si esegue la distribuzione in un test o si carica nell'App Store, è necessario completare i passaggi in questa pagina:

- In **iOS Dev Center**:
  - Gli [ID app](#App_IDs) sono stati creati.
  - [Gruppi di app](#App_Groups) configurati (se richiesto).
  - Profili di provisioning di distribuzione creati

- Nella soluzione:

  - Verificare [che gli ID bundle e i riferimenti al progetto](~/ios/watchos/get-started/installation.md) siano impostati.
  - Verificare che le icone siano [configurate correttamente](~/ios/watchos/app-fundamentals/icons.md).
  - Verificare che i numeri di versione del bundle corrispondano in tutti i progetti.
  - Configurare i **diritti. plist** per i gruppi di app, se necessario.

- Seguire quindi le istruzioni per:
  - [Eseguire la distribuzione in un Apple Watch per il testing](~/ios/watchos/deploy-test/device.md)o
  - [Caricare nell'App Store](~/ios/watchos/deploy-test/appstore.md).

<a name="App_IDs"></a>

## <a name="app-ids"></a>ID app

Come illustrato nelle [istruzioni di installazione](~/ios/watchos/get-started/installation.md), tutti e tre i progetti in un'app Watch hanno ID bundle correlati, ad esempio:

- Progetto unificato Novell. iOS-`com.xamarin.WatchKitCatalog`
- Progetto di estensione WatchKit-`com.xamarin.WatchKitCatalog.watchkitextension`
- Guarda il progetto dell'app-`com.xamarin.WatchKitCatalog.watchkitapp`

Per tutti e tre i progetti è necessario un profilo di provisioning di distribuzione corrispondente, usando ID app esplicitamente per ognuno di essi o un ID app con caratteri jolly.

### <a name="explicit-app-ids"></a>ID app espliciti

Creare un **ID app** per l'ID bundle di ogni progetto, che sarà simile al seguente in iOS Dev Center:

![ID bundle in iOS Dev Center](images/appids-specific-sml.png)

Quando si creano o si configurano gli ID app, ricordarsi di abilitare le funzionalità specifiche richieste dall'app. Potrebbero essere incluse le notifiche push e i gruppi di app.

Sarà necessario creare un profilo di provisioning di distribuzione per ogni ID app.

### <a name="wildcard-app-id"></a>ID app con caratteri jolly

In alternativa, è possibile creare un **ID app** con caratteri jolly che corrisponde a tutti e tre i progetti, ad esempio `com.xamarin.*` .

Si noti che alcune funzionalità non possono essere usate con un ID app con caratteri jolly, ad esempio le notifiche push. Se l'app richiede queste funzionalità, è necessario creare ID app espliciti.

Per la distribuzione, è necessario creare solo un profilo di provisioning di distribuzione per l'ID app con caratteri jolly.

<a name="App_Groups"></a>

## <a name="app-groups"></a>Gruppi di app

È possibile usare un gruppo di app per condividere i dati tra l'app iOS e l'estensione Watch. È necessario assicurarsi che la soluzione abbia:

- Configurare il **gruppo di app** nella sezione certificati del portale per sviluppatori Apple **, identificatori & profili** .

- **Gruppi di app** abilitati (e fornito l'ID del gruppo *di* **app**) nell'app iOS e nell' **ID app** dell'estensione Watch e in **titles. plist**.

### <a name="certificates-identifiers--profiles"></a>Certificati, identificatori & profili

Per usare un gruppo di app, creare una voce nella schermata **gruppi di app** . Nell'esempio seguente il gruppo viene denominato con lo stesso stile DNS inverso, comunemente usato per gli ID app, ma con il `group.` prefisso (obbligatorio):

![Identificatore](images/appgroups-new-sml.png)

Il gruppo di app verrà quindi visualizzato nell'elenco:

![Elenco di identificatori](images/appgroups-setup-sml.png)

Una volta creato il gruppo, è possibile farvi riferimento nella configurazione dell' **ID app** . Ricordarsi di includere sia l'app iOS sia gli **ID app**di estensione di controllo.

![Configurazioni disponibili](images/appgroups-sml.png)

**Non** abilitare i gruppi di app nell'ID app Apple Watch. Non è necessario che sia abilitato nell'espressione di controllo.

### <a name="entitlementsplist"></a>Entitlements.plist

Alcune funzionalità delle app, ad esempio Per i gruppi di app è necessario impostare i diritti.
Fare doppio clic per modificare il file **titles. plist** nei progetti seguenti:

- progetto di app iOS
- Progetto di estensione Watch

.![Editor dei diritti. plist](images/entitlements-plist-sml.png)

**Non** abilitare i diritti nel progetto dell'app Watch. Non è necessario che sia abilitato nell'espressione di controllo.

## <a name="related-links"></a>Collegamenti correlati

- [Guida all'invio di Apple WatchKit](https://developer.apple.com/app-store/watch/)
