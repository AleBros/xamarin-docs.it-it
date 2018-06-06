---
title: Distribuzione e test watchOS App con Xamarin
description: Questo documento viene descritto come distribuire e testare watchOS compilate con Xamarin. Questa procedura guidata fornisce un elenco di controllo di distribuzione, vengono illustrati esplicita e con caratteri jolly app ID e si esaminano i gruppi di app.
ms.prod: xamarin
ms.assetid: 98257399-E9B3-4BAB-9204-0E89117DEA6D
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/17/2017
ms.openlocfilehash: 778583456e74bb7ed3a85dce96bcdbc487aef57a
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/05/2018
ms.locfileid: "34790942"
---
# <a name="deploying-and-testing-watchos-apps-with-xamarin"></a>Distribuzione e test watchOS App con Xamarin

## <a name="deployment-checklist"></a>Elenco di controllo distribuzione

Se si desidera distribuire a un test delle espressioni di controllo o caricare l'archivio di App, è necessario completare i passaggi in questa pagina:

- Nel **iOS Dev Center**:
  - [ID dell'app](#App_IDs) sono stati creati.
  - [Gruppi di app](#App_Groups) configurata (se richiesto).
  - Profili di Provisioning distribuzione creato

- Nella soluzione:

  - Verificare il [ID Bundle e i riferimenti progetto](~/ios/watchos/get-started/installation.md) sono impostati.
  - Controllare le icone sono [configurato correttamente](~/ios/watchos/app-fundamentals/icons.md).
  - Verificare la corrispondenza di numeri di versione bundle in tutti i progetti.
  - Configurare il **Entitlements.plist** per gruppi di App (se richiesto).

* Seguire le istruzioni per:
  - [Distribuire un Apple Watch per il testing](~/ios/watchos/deploy-test/device.md), o
  - [Caricare nell'App Store](~/ios/watchos/deploy-test/appstore.md).

<a name="App_IDs"/>

## <a name="app-ids"></a>ID dell'App

Come descritto nel [istruzioni di installazione](~/ios/watchos/get-started/installation.md), tutti e tre i progetti in un'applicazione di espressioni di controllo correlate ID Bundle, ad esempio:

- Progetto unificato di xamarin. IOS- `com.xamarin.WatchKitCatalog`
- Progetto di estensione WatchKit- `com.xamarin.WatchKitCatalog.watchkitextension`
- Progetto di App - espressioni di controllo `com.xamarin.WatchKitCatalog.watchkitapp`

Tutti i tre progetti richiedono un corrispondente distribuzione profilo di Provisioning, utilizzando in modo esplicito per ogni ID dell'App, o un carattere jolly ID App.

### <a name="explicit-app-ids"></a>ID dell'App esplicita

Creare un **ID App** per l'ID Bundle ogni ogni progetto (che sarà simile al seguente nel centro per sviluppatori iOS):

![Gli ID Bundle nel centro per sviluppatori iOS](images/appids-specific-sml.png)

Quando si crea o configura l'ID dell'App, ricordare di abilitare l'app richiede caratteristiche specifiche. Ad esempio le notifiche push e gruppi di app.

È necessario creare un profilo di Provisioning della distribuzione per ogni ID. App

### <a name="wildcard-app-id"></a>ID dell'App con caratteri jolly

In alternativa, è possibile creare un carattere jolly **ID App** che corrisponde a tutti e tre i progetti, ad esempio `com.xamarin.*`.

Si noti che alcune funzionalità possono essere utilizzate con un carattere jolly dell'ID App (ad esempio le notifiche push). Se l'app richiede queste funzionalità è necessario creare ID App esplicita.

Per la distribuzione, è solo necessario creare una distribuzione profilo di Provisioning per il carattere jolly ID App.

<a name="App_Groups" />

## <a name="app-groups"></a>Gruppi di app

È possibile utilizzare un gruppo di App per condividere dati tra App iOS per la e l'estensione di espressioni di controllo. È necessario assicurarsi che la soluzione contiene:

- Configurato il **gruppo App** nel portale per sviluppatori Apple **certificati, profili & identificatori** sezione.

- Abilitato **gruppi di App** (e fornito il **ID gruppo App**) in *entrambi* iOS App e l'estensione di espressioni di controllo **ID App** e  **Entitlements.plist**.

### <a name="certificates-identifiers--profiles"></a>I certificati, gli identificatori e i profili

Per utilizzare un gruppo di App, creare una voce di **gruppi di App** dello schermo. Nell'esempio seguente il gruppo è denominato con lo stesso stile inversa DNS che viene comunemente utilizzato per l'ID dell'App, ma con il `group.` prefisso (obbligatorio):

![L'identificatore](images/appgroups-new-sml.png)

Il gruppo dell'applicazione verrà quindi visualizzata nell'elenco:

![L'elenco di identificatore](images/appgroups-setup-sml.png)

Una volta creato il gruppo, è possibile farvi riferimento nel **ID App** configurazione. È necessario includerla di iOS App e l'estensione di espressioni di controllo **ID App**.

![Consifurations disponibili](images/appgroups-sml.png)

Eseguire **non** abilitare gruppi di App nell'ID dell'App di Apple Watch. Non è necessario attivare questa opzione nel controllo stesso.

### <a name="entitlementsplist"></a>Entitlements.plist

Alcune funzionalità dell'applicazione (ad es. Gruppi di App) è necessario impostare i diritti.
Fare doppio clic per modificare il **Entitlements.plist** file in questi progetti:

- progetto di App iOS
- Progetto di estensione delle espressioni di controllo

.![L'editor Entitlements.plist](images/entitlements-plist-sml.png)

Eseguire **non** abilitare i diritti nel progetto di applicazione delle espressioni di controllo. Non è necessario attivare questa opzione nel controllo stesso.

## <a name="related-links"></a>Collegamenti correlati

- [Guida di Apple WatchKit invio](https://developer.apple.com/app-store/watch/)
