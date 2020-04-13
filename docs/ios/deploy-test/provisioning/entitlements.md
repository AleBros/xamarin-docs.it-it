---
title: Uso degli entitlement in Xamarin.iOS
description: Gli entitlement sono funzionalità speciali delle app e autorizzazioni di sicurezza concesse alle applicazioni configurate correttamente per usarli.
ms.prod: xamarin
ms.assetid: 8A3961A2-02AB-4228-A41D-06CB4108D9D0
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 08/13/2018
ms.openlocfilehash: f6a38aea1e46e1165bb36d83e75e24769de0a1e2
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2020
ms.locfileid: "75655354"
---
# <a name="working-with-entitlements-in-xamarinios"></a>Uso degli entitlement in Xamarin.iOS

_Gli entitlement sono funzionalità speciali delle app e autorizzazioni di sicurezza concesse alle applicazioni configurate correttamente per il loro uso._

In iOS le app vengono eseguite in una _sandbox_, che fornisce un set di regole che limitano l'accesso tra l'applicazione e determinate risorse di sistema o dati utente. Gli _entitlement_ vengono usati per richiedere al sistema di espandere la sandbox per fornire all'app funzionalità aggiuntive.

Per estendere le funzionalità dell'app, è necessario specificare un entitlement nel file Entitlements.plist dell'app. Possono essere estese solo determinate funzionalità, elencate nella guida [Uso delle funzionalità](~/ios/deploy-test/provisioning/capabilities/index.md) e descritte [sotto](#entitlement-key-reference). Gli entitlement vengono passati al sistema come coppia di chiave/valore e in genere ne è necessario solo uno per ogni funzionalità. Le chiavi e i valori specifici sono descritti nella sezione Riferimento [chiave autorizzazione](#entitlement-key-reference) più avanti in questa guida.
Visual Studio per Mac e Visual Studio offrono un'interfaccia chiara per l'aggiunta di entitlement in un'app Xamarin.iOS tramite l'editor Entitlements.plist.
Questa guida illustra l'editor Entitlements.plist e come usarlo. Fornisce anche informazioni di riferimento su tutti gli entitlement che possono essere aggiunti a un progetto iOS per ogni funzionalità.

## <a name="entitlements-and-provisioning"></a>Entitlement e provisioning

Il file Entitlements.plist viene usato per specificare gli entitlement e per firmare il bundle dell'applicazione.

È tuttavia necessario un provisioning aggiuntivo per assicurarsi che il codice dell'app sia firmato correttamente. Il profilo di provisioning usato deve contenere un ID app con la funzionalità necessaria abilitata. Per informazioni su come procedere, vedere la guida [Uso delle funzionalità](~/ios/deploy-test/provisioning/capabilities/index.md).

> [!IMPORTANT]
> Il file Entitlements.plist consente di inserire le proprietà corrette per un'applicazione usando le funzionalità, ma non riesce a generare un profilo di provisioning perché non è collegato a un account per sviluppatore Apple. Sarà tuttavia necessario generare un profilo di provisioning usando il portale per sviluppatori per distribuire l'applicazione.

## <a name="set-entitlements-in-a-xamarinios-project"></a>Impostare gli entitlement in un progetto Xamarin.iOS

Durante la definizione dell'ID app, oltre a selezionare e configurare i servizi dell'applicazione richiesti, è anche necessario configurare gli entitlement nel progetto Xamarin.iOS modificando i file **Info.plist** e **Entitlements.plist**.

# <a name="visual-studio-for-mac"></a>[Visual Studio per Mac](#tab/macos)

Per configurare gli entitlement in Visual Studio per Mac, seguire questa procedura:

1. Nel **riquadro Soluzione**fare doppio clic sul file **Info.plist** per aprirlo.
2. Nella sezione **Identity** immettere un nome per l'applicazione e immettere **l'identificatore del pacchetto** creato al momento della definizione dell'ID app:

    ![Immettere un identificatore del bundle](entitlements-images/servicexs01-sml.png)

3. Salvare le modifiche apportate al file **Info.plist**.
4. Nel **riquadro Soluzione**fare doppio clic sul file **Entitlements.plist** per aprirlo e modificarlo:

    ![Modifica degli entitlement](entitlements-images/servicexs02-sml.png)

5. Selezionare e configurare gli entitlement richiesti per l'applicazione Xamarin.iOS in modo che corrispondano alla configurazione definita durante la creazione dell'ID app.
6. Salvare le modifiche apportate al file **Entitlements.plist**.

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

Per configurare gli entitlement in Visual Studio, seguire questa procedura:

1. In **Esplora soluzioni**fare doppio clic sul file **Info.plist** per aprirlo e modificarlo.
2. Nella sezione **Destinazione applicazione iOS** immettere un nome per l'applicazione e l'**Identificatore del bundle** creato in fase di definizione dell'ID app:

    ![Impostazione dell'identificatore del bundle](entitlements-images/servicevs01-sml.png)

3. Salvare le modifiche apportate al file **Info.plist**.
4. In **Esplora soluzioni**fare doppio clic sul file **Entitlements.plist** per aprirlo:

    ![Modifica degli entitlement](entitlements-images/servicevs02-sml.png)

    È anche possibile fare clic con il pulsante destro del mouse sul file **Entitlements.plist** e scegliere **Apri con...** l'editor Origine XML che consente di impostare la proprietà Entitlement e il valore della chiave come descritto nella sezione Riferimento sulla [chiave](#entitlement-key-reference) di autorizzazione riportata di seguito.

5. Selezionare e configurare gli entitlement richiesti per l'applicazione Xamarin.iOS in modo che corrispondano alla configurazione definita durante la creazione dell'ID app.
6. Salvare le modifiche apportate al file **Entitlements.plist**.

-----

## <a name="adding-a-new-entitlementsplist-file"></a>Aggiunta di un nuovo file Entitlements.plist

Gli entitlement vengono aggiunti a un'app tramite il file Entitlements.plist. Questo file è incluso nel progetto Xamarin.iOS per impostazione predefinita, ma potrebbe mancare nei progetti meno recenti.

Per aggiungere un file Entitlements.plist a Xamarin.iOS, seguire questa procedura:

1. Fare clic con il pulsante destro del mouse sul file di progetto e passare ad **Aggiungi > Nuovo file**:

    ![Menu di scelta rapida per l'aggiunta di file](entitlements-images/image1-sml.png)
2. Nella finestra di dialogo Nuovo file selezionare **iOS > Elenco proprietà** e assegnargli il nome Entitlements:

    ![Finestra di dialogo Nuovo file](entitlements-images/image2-sml.png)

## <a name="entitlement-key-reference"></a>Informazioni di riferimento sulle chiavi degli entitlement

Le chiavi degli entitlement possono essere aggiunte tramite il pannello Source (Origine) dell'editor Entitlements.plist. Le chiavi necessarie vengono in genere aggiunte quando si usa l'editor Entitlements.plist, ma sono elencate sotto come riferimento.

### <a name="wallet"></a>Wallet

- **Descrizione**: conosciuta comunemente come Passbook, Wallet è un'app per l'archiviazione e la gestione dei pass. Questi pass possono essere carte di credito, carte cliente, carte d'imbarco o biglietti.

  - **Identificatore tipo di pass**
    - **Chiavi**: com.apple.developer.pass-type-identifiers
    - **Stringa**:`$(TeamIdentifierPrefix)*`

- **Note**:
  - Permetterà all'app di consentire tutti i tipi di pass. Per limitare l'app e consentire solo un subset di tipi di pass per il team, impostare il valore della stringa su: `$(TeamIdentifierPrefix)pass.$(CFBundleIdentifier)`

  Dove pass.$(CFBundleIdentifier) è l'ID del pass creato [sopra](~/ios/platform/passkit.md)

### <a name="icloud"></a>iCloud

- **Descrizione**: iCloud offre agli utenti iOS un modo semplice e pratico per archiviare contenuti e condividerli tra i dispositivi. Gli sviluppatori possono usare iCloud in quattro modi per offrire ai propri utenti un mezzo di archiviazione: archiviazione chiave-valore, archiviazione UIDocument, CoreData e uso diretto di CloudKit per l'archiviazione di singoli file e directory. Per altre informazioni su questi argomenti, vedere la guida introduttiva a iCloud.

  - **Documenti iCloud e CloudKit**
    - **Chiavi**: com.apple.developer.ubiquity-container-identifiers
    - **Stringa**:`$(TeamIdentifierPrefix)$(CFBundleIdentifier)`
  - **Archiviazione chiave-valore iCloud**
    - **Chiave**: com.apple.developer.ubiquity-kvstore-identifier
    - **Stringa**:`$(TeamIdentifierPrefix)$(CFBundleIdentifier)`

- **Note**:
  - Per individuare la stringa `$(TeamIdentifierPrefix)`, accedere a developer.apple.com e visitare **Member Center > Account utente > Developer Account Summary (Riepilogo account per sviluppatore)** per ottenere l'ID del team (o l'ID individuale per i singoli sviluppatori). Sarà una stringa di 10 caratteri (ad esempio, A93A5CM278).
  - La stringa `$(CFBundleIdentifier)` inizia con `iCloud` e viene impostata quando il contenitore iCloud viene creato seguendo i passaggi della guida [Uso delle funzionalità](~/ios/deploy-test/provisioning/capabilities/icloud-capabilities.md).
  - Si possono usare i segnaposto $`(TeamIdentifierPrefix)` e `$(CFBundleIdentifier)` che verranno sostituiti con i valori corretti in fase di compilazione.

> [!IMPORTANT]
> Apple [fornisce strumenti](https://developer.apple.com/support/allowing-users-to-manage-data/) per aiutare gli sviluppatori a gestire correttamente il Regolamento generale sulla protezione dei dati (GDPR) dell'Unione Europea.

### <a name="app-groups"></a>Gruppi di app

- **Descrizione**: un gruppo di app consente a diverse applicazioni o a un'applicazione e alle relative estensioni di accedere a un percorso di archiviazione file condiviso.

  - **Chiave**: com.apple.security.application-groups
  - **Stringa**: group.$(CFBundleIdentifier)

### <a name="apple-pay"></a>Apple Pay

- **Descrizione**: Apple Pay consente agli utenti di pagare beni tramite il dispositivo iOS.
  - **Chiave**: com.apple.developer.in-app-payments
  - **Stringa**: merchant.your.mechantid

### <a name="push-notifications"></a>Notifiche push

- **Chiave**: aps-environment
- **Stringa** `development` : o`production`

### <a name="siri"></a>Siri

- **Descrizione**: SiriKit consente a un'app iOS di fornire servizi accessibili a Siri e all'app Mappe su un dispositivo iOS usando le estensioni app e i nuovi framework Intent e Interfaccia utente Intent. Per altre informazioni, vedere la guida introduttiva a SiriKit.
  - **Chiave**: com.apple.developer.siri

### <a name="personal-vpn"></a>VPN personale

- **Chiave**: com.apple.developer.networking.vpn.api
- **Stringa**: allow-vpn

### <a name="keychain-sharing"></a>Condivisione del keychain

- **Descrizione**: la condivisione del keychain consente agli sviluppatori di app di condividere le password archiviate nel keychain di un dispositivo con le altre app sviluppate dallo stesso team. L'accesso può essere limitato passando un identificatore di gruppo di accesso keychain nella stringa.
  - **Chiave**: keychain-access-groups
  - **Stringa**: $(AppIdentifierPrefix) $(CFBundleIdentifier)

### <a name="inter-app-audio"></a>Audio Inter-App

- **Descrizione**: Audio Inter-App consente agli sviluppatori di trasmettere l'audio tra le app.
  - **Chiave**: inter-app-audio
  - **Valore booleano**: YES

### <a name="associated-domains"></a>Domini associati

- **Descrizione**: i domini associati che devono essere gestiti come collegamenti universali devono essere passati con questo entitlement. I collegamenti universali possono essere implementati per consentire il deep linking tra l'app e il sito Web. È consigliabile specificare una voce per ogni dominio supportato dall'app e ogni voce deve iniziare con `applinks:`
  - **Chiave**: com.apple.developer.associated-domains
  - **Stringa**: webcredentials:example.com

### <a name="data-protection"></a>Protezione dei dati

- **Descrizione**: l'abilitazione della protezione dati usa l'hardware di crittografia predefinito per archiviare i dati sensibili usati nell'app in un formato crittografato. Per impostazione predefinita, il livello di protezione viene impostato sulla protezione completa, in cui i file sono accessibili solo quando il dispositivo è sbloccato.
  - **Chiave**: com.apple.developer.default-data-protection
  - **Stringa**: NSFileProtectionComplete

### <a name="homekit"></a>HomeKit

- **Descrizione**: il framework HomeKit fornisce una piattaforma per l'impostazione, la configurazione e la gestione dei dispositivi di domotica supportati da un solo dispositivo iOS. Per altre informazioni sull'uso di HomeKit, vedere la guida introduttiva a HomeKit.
  - **Chiave**: com.apple.developer.homekit
  - **Valore booleano**: YES

### <a name="healthkit"></a>HealthKit

- **Descrizione**: HealthKit è un framework introdotto in iOS 8 che fornisce un archivio dati centralizzato, coordinato e sicuro per le informazioni relative alla salute. Per altre informazioni sull'uso di HealthKit, vedere la guida introduttiva a HealthKit.
  - **Chiave**: com.apple.developer.healthkit
  - **Valore booleano**: YES

### <a name="wireless-accessory-configuration"></a>Configurazione accessori wireless

- **Descrizione**: l'uso di Configurazione accessori wireless consente all'app di configurare gli accessori Wi-Fi MFi
  - **Chiave**: com.apple.external-accessory.wireless-configuration
  - **Valore booleano**: YES

### <a name="classkit"></a>ClassKit

- **Descrizione**: ClassKit consente ai docenti di visualizzare l'avanzamento degli studenti nelle attività assegnate nell'app.
  - **Key**: com.apple.developer.ClassKit-environment
  - **Stringa** `development` : o`production`

## <a name="summary"></a>Riepilogo

Questa guida ha illustrato gli entitlement e come usarli in Visual Studio per Mac e in Visual Studio. Ha anche fornito informazioni di riferimento sulle coppie chiave/valore per ogni funzionalità.
