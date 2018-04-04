---
title: Distribuzione di App Store
description: Distribuzione di App di espressioni di controllo per l'archivio di App
ms.prod: xamarin
ms.assetid: DBE16040-70D2-4F61-B5F3-C8D213DBC754
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/17/2017
ms.openlocfilehash: a3c68a0720fbe18f35731cf1a19248c00f748877
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2018
---
# <a name="deploying-to-the-app-store"></a>Distribuzione di App Store

> [!IMPORTANT]
> Assicurarsi di consultare [Watch Kit invio Guida Apple](https://developer.apple.com/app-store/watch/)e vedere il [risoluzione dei problemi](#Troubleshooting) sezione per qualsiasi tipo di problema.

- Assicurarsi di che disporre di:
  - [**I profili di Provisioning distribuzione** ](#provisioning) creato per i progetti.
  - Il **destinazione distribuzione** (`MinimumOSVersion`) per iOS padre è impostata su app **8.2** o versioni precedenti (8.3 non supportato).

- In [ **iTunes Connect**](#iTunes_Connect):

  - Creare iOS per la voce dell'app (o aggiungere un **nuova versione** per un'app esistente).
  - Aggiungere schermate e icona.

- Quindi nel [Visual Studio per Mac](#xamarin_studio) (Visual Studio non è attualmente supportata):

  - L'app iOS di mouse e scegliere **imposta come progetto di avvio**.
  - Modificare il **App Store** configurazione.
  - Utilizzare il **archivio** funzionalità di creazione di un archivio di applicazione.

- Infine, passare a [Xcode 6.2 +](#xcode)

  - Passare al **finestra > libreria** e scegliere **archivi**.
  - Selezionare l'applicazione e archiviazione dall'elenco.
  - (Facoltativo) **Convalidare...**  l'archivio.
  - **Invia...**  l'archiviazione e seguire i passaggi per caricare in iTunes connettono per la revisione e approvazione.

Leggi i suggerimenti specifici correlati a tali elementi seguenti. Vedere il [Troubleshooting](#Troubleshooting) sezione in caso di problemi.

<a name="provisioning" />

## <a name="distribution-provisioning-profiles"></a>Distribuzione di profili di Provisioning

Per compilare per la distribuzione di App Store è necessario creare un **profilo di Provisioning distribuzione** per ogni ID di App nella soluzione.

Se si dispone di un carattere jolly ID App, *sarà necessario un solo profilo di Provisioning*; ma se si dispone di un ID App separate per ogni progetto, è necessario un profilo di provisioning per ogni ID di App:

![](appstore-images/provisioningprofile-distribution-sml.png "Il profilo di distribuzione di App Store")

Dopo aver creato tutti e tre i profili, questi verranno visualizzati nell'elenco. Ricordarsi di scaricare e installare ciascuno di essi (facendo doppio clic su di essa):

![](appstore-images/provisioningprofiles-sml.png "L'elenco dei profili disponibili")

È possibile verificare il profilo di provisioning nel **opzioni progetto** selezionando il **compilazione > firma Bundle iOS** dello schermo e selezionando il **AppStore | iPhone** configurazione.

Il **profilo di Provisioning** elenco verranno visualizzati tutti i profili corrispondenti, si noterà che i profili corrispondenti che sono stati creati in questo elenco a discesa.

![](appstore-images/options-selectprofile-sml.png "La finestra di dialogo firma Bundle iOS")

<a name="iTunes_Connect"/>

## <a name="itunes-connect"></a>iTunes Connect

Seguire il [Cenni preliminari sulla distribuzione di app](~/ios/deploy-test/app-distribution/index.md), in particolare:

- [Configurazione di un'applicazione in iTunes Connect](~/ios/deploy-test/app-distribution/app-store-distribution/itunesconnect.md)
- [Pubblicazione nell'App Store](~/ios/deploy-test/app-distribution/app-store-distribution/publishing-to-the-app-store.md)

Quando si configura l'applicazione in iTunes Connect, non dimenticare di aggiungere l'icona e schermate:

![](appstore-images/itunesconnect-watch-sml.png "L'icona e schermate in iTunes Connect")

Il file dell'icona deve essere 1024 x 1024 pixel e disporrà di una maschera circolare applicata quando viene visualizzato. L'icona non deve avere un canale alfa.

Almeno una schermata, è necessario, fino a cinque possono essere inviate.
Devono essere 312 x 390 pixel e dimostrare l'applicazione di espressioni di controllo in azione.
Il simulatore di 42mm espressioni di controllo consente di acquisire schermate queste dimensioni.


<a name="xamarin_studio" />

## <a name="visual-studio-for-mac"></a>Visual Studio per Mac

1. Verificare che l'app iOS sia il progetto di avvio. In caso contrario, fare doppio clic su per impostarlo:

  ![](appstore-images/xs-startup.png "Impostazione del progetto di avvio")

2. Scegliere il **AppStore** configurazione di compilazione:

  ![](appstore-images/xs-appstore.png "La configurazione della build AppStore")

3. Scegliere il **compilazione > archivio** voce di menu per avviare il processo di archiviazione:

  ![](appstore-images/xs-archive.png "Dal menu Compila")

È anche possibile scegliere di **Vista > archivi...**  voce di menu per visualizzare gli archivi che sono stati creati in precedenza.

  ![](appstore-images/xs-archives-sml.png "La visualizzazione di archivi")

<a name="xcode" />

## <a name="xcode"></a>Xcode

Xcode visualizzerà automaticamente gli archivi creati in Visual Studio per Mac.

1. Avviare Xcode e poi scegliere **finestra > libreria**:

  ![](appstore-images/xc-organizer.png "Menu finestra")

2. Passare il **archivi** e selezionare l'archivio in cui è stato creato con Visual Studio per Mac:

  ![](appstore-images/xc-archives.png "Scheda archivi")

3. Facoltativamente **convalidare...**  archivio, quindi scegliere **Invia...**  per caricare l'app a iTunes Connect.

4. Scegliere il team di sviluppo (se si appartiene a più di uno) e quindi confermare l'inoltro:

  ![](appstore-images/xc-submit1.png "La sezione del team di sviluppo")

5. Visitare iTunes Connect per vedere il file caricato binario. Passare alla pagina di configurazione dell'applicazione e scegliere **versione provvisoria** dal menu in alto per visualizzare il **compilazioni** elenco:

  [![](appstore-images/itc-prerelease-sml.png "La pagina di configurazione di App in iTunes Connect")](appstore-images/itc-prerelease.png#lightbox)

È quindi possibile inviare l'app per l'approvazione sul **versioni** pagina. Consultare la [Cenni preliminari sulla distribuzione di app iOS](~/ios/deploy-test/app-distribution/index.md) per ulteriori informazioni.


## <a name="troubleshooting"></a>Risoluzione dei problemi

Ecco alcuni errori che possono verificarsi durante l'invio all'archivio di App e i passaggi da eseguire per correggerli.

### <a name="archive-menu-option-is-not-visible-in-visual-studio-for-mac"></a>Opzione di menu di archivio non è visibile in Visual Studio per Mac

Seguire il [passaggi precedenti](#xamarin_studio) per configurare la soluzione per l'archiviazione. Se è possibile impostare correttamente il progetto di avvio, verificare che la configurazione di compilazione prima di tutto è impostata su Debug o rilascio prima di tentare di modificare il progetto di avvio. Impostare la configurazione della build al **AppStore**.

### <a name="invalid-icon"></a>Icona Non valido

```csharp
Invalid Icon - The watch application '...watchkitextension.appex/WatchApp.app'
contains an icon file '...watchkitextension.appex/WatchApp.app/AppIcons27.5x27.5@2x.png'
with an alpha channel. Icons should not have an alpha channel.
```

Seguire il [istruzioni per rimuovere il canale alfa](~/ios/watchos/troubleshooting.md) da icone.

### <a name="cfbundleversion-mismatch"></a>Mancata corrispondenza CFBundleVersion

```csharp
CFBundleVersion Mismatch. The CFBundleVersion value '1' of watch application
'...watchkitextension.appex/WatchApp.app' does not match the CFBundleVersion
value '1.0' of its containing iOS application `YouriOS.app`.
```

Tutti i progetti nella soluzione di App iOS, l'estensione di espressioni di controllo e l'applicazione di espressioni di controllo - devono utilizzare lo stesso numero di versione. Modificare ciascun **Info. plist** file in modo che il numero di versione corrisponde esattamente.

### <a name="missing-icons"></a>Mancanza delle icone

```csharp
Missing Icons. No icons found for watch application '...watchkitextension.appex/WatchApp.app'.
Please make sure that its Info.plist file includes entries for CFBundleIconFiles.
```

Seguire le istruzioni di [funziona con icone](~/ios/watchos/app-fundamentals/icons.md) per aggiungere tutte le immagini necessarie per il progetto di applicazione delle espressioni di controllo.

### <a name="missing-icon"></a>Icona mancanti

```csharp
Missing Icon. The watch application '...watchkitextension.appex/WatchApp.app'
is missing icon with name pattern '*44x44@2x.png' (Home Screen 42mm).
```

Verificare di aver la versione più recente di Visual Studio per Mac e che il **AppIcons.appiconset** contiene un set completo di immagini. Se questo errore viene ancora visualizzato, visualizzare l'origine del **Contents.json** per confermare contiene una voce per tutte le immagini necessarie. In alternativa, dopo aver verificato la si utilizza la versione più recente di Xamarin, eliminare e ricreare il **AppIcons.appiconset**.

> [!IMPORTANT]
> È presente un bug noto in Visual Studio per il supporto di icona espressioni di controllo del Mac: è previsto che un'immagine di 88 x 88 pixel per la **29x29@3x** immagine (che deve essere 87 x 87 pixel).


Non è possibile risolvere questo problema in Visual Studio per Mac - modificare la risorsa di immagine in Xcode o modificare manualmente il **Contents.json** file (in modo che corrisponda [in questo esempio](https://github.com/xamarin/monotouch-samples/blob/master/WatchKit/WatchKitCatalog/WatchApp/Resources/Images.xcassets/AppIcons.appiconset/Contents.json#L126-L132)).



### <a name="invalid-watchkit-support"></a>Supporto WatchKit non valido

```csharp
Invalid WatchKit Support - The bundle contains an invalid implementation of WatchKit.
The app may have been built or signed with non-compliant or pre-release tools.
```

Questo messaggio può essere visualizzato durante la convalida e di invio, o in un messaggio e-mail automatico da iTunes Connect dopo un'operazione di caricamento apparentemente esito positivo.
<!--
Ensure you are using the latest version of Xcode and Xamarin's tools.
-->
> [!IMPORTANT]
> È necessario **archivio** dell'app in Visual Studio per Mac e quindi passare a Xcode 6.2 + convalidare e caricare in iTunes Connect.


Utilizzare il canale Xamarin stabile e Xcode 6.2 +.



### <a name="invalid-provisioning-profile"></a>Profilo di Provisioning non valido

```csharp
Invalid Provisioning Profile. The provisioning profile included in the bundle
...iOSWatchApp.watchkitapp [iOSWatchApp.app/PlugIns/...iOSWatchApp.watchkitextension.appex/WatchApp.app]
is invalid. [Missing code-signing certificate.]
```

**I profili di Provisioning di distribuzione** devono essere specificati per tutte e tre i progetti in una soluzione di applicazione delle espressioni di controllo: App iOS, l'estensione di espressioni di controllo e l'applicazione di espressioni di controllo - un in modo esplicito (tre profili) o tramite un profilo con caratteri jolly singolo. Verificare che i profili di provisioning esistano nel centro per sviluppatori iOS e di aver scaricato e averli aggiunti al Mac.

### <a name="invalid-code-signing-entitlements"></a>I diritti di accesso di codice non valido

```csharp
ITMS-90046: Invalid Code Signing Entitlements. Your application bundle's signature contains
code signing entitlements that are not supported on iOS. Specifically, value
'...watchkitextension' for key 'application-identifier' in '...watchkitextension'
is not supported. The value should be a string startign with your TEAMID, followed
by a dot '.' followed by the bundle identifier.
```

Verificare che i profili di provisioning sono configurazione correttamente nel centro per sviluppatori di Apple, e di aver scaricato e installato in. Controllare inoltre impostate per la finestra delle proprietà del Mac per ogni progetto in Visual Studio.

### <a name="invalid-architecture"></a>Architettura non valido

```csharp
Invalid architecture: Apps that include an app extension
and framework must support arm64.
```

È possibile aggiungere solo espressioni di controllo app [Unified API (64 bit)](~/cross-platform/macios/unified/index.md) App xamarin. IOS.
Pulsante destro del mouse sul progetto di app iOS, quindi andare al **Opzioni > compilare > compilazione iOS > scheda Avanzate** e assicurarsi che il **architetture supportate** per iPhone AppStore-configurazione include **ARM64** (ad es. **ARMv7 + ARM64**).

### <a name="this-bundle-is-invalid"></a>Questo pacchetto non è valido.

```csharp
ITMS-90068: This bundle is invalid. The value provided for the key
MinimumOSVersion '8.3' is not acceptable.
```

L'applicazione iOS padre deve avere il MinimumOSVersion impostato su '8.2' o versioni precedenti.

### <a name="non-public-api-usage"></a>Utilizzo delle API non pubblici

```csharp
Your app contains non-public API usage.
Please review the errors, and resubmit your application.
```

Assicurarsi di che utilizzare la versione più recente di strumenti Xcode e Xamarin.
Il codice non deve accedere le API non è pubblica.

### <a name="build-error-mt5309"></a>MT5309 errore di compilazione

```csharp
Error MT5309: Native linking error: clang: error: no such file or directory:
```

Questo errore è probabilmente il risultato della presenza di rinominare l'installazione di Xcode da **Xcode.app**. Ad esempio, questo errore si verifica se si rinomina l'installazione di **XCode 6.2.app**.



## <a name="related-links"></a>Collegamenti correlati

- [Guida di Apple WatchKit invio](https://developer.apple.com/app-store/watch/)
