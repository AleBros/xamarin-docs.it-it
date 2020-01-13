---
title: Distribuzione delle app watchos nell'App Store
description: Questo documento descrive come distribuire le app watchos compilate con Novell nell'App Store. Vengono esaminati i profili di provisioning della distribuzione e iTunes Connect, oltre ad alcuni suggerimenti per la risoluzione dei problemi.
ms.prod: xamarin
ms.assetid: DBE16040-70D2-4F61-B5F3-C8D213DBC754
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/17/2017
ms.openlocfilehash: bbf580007f4d149501efe424f0e36178a49f6aa5
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73028366"
---
# <a name="deploying-watchos-apps-to-the-app-store"></a>Distribuzione delle app watchos nell'App Store

> [!IMPORTANT]
> Assicurarsi di consultare la [Guida all'invio del kit di controllo di Apple](https://developer.apple.com/app-store/watch/)e vedere la sezione relativa alla [risoluzione dei](#troubleshooting) problemi eventualmente disponibili.

- Assicurarsi di disporre di:
  - [**Profili di provisioning di distribuzione**](#provisioning) creati per i progetti.
  - La **destinazione di distribuzione** (`MinimumOSVersion`) per l'app padre iOS impostata su **8,2** o versioni precedenti (8,3 non è supportata).

- In [**iTunes Connect**](#iTunes_Connect):

  - Creare la voce dell'app iOS o aggiungere una **nuova versione** a un'app esistente.
  - Aggiungere le schermate e l'icona del controllo.

- In [Visual Studio per Mac](#xamarin_studio) (Visual Studio non è attualmente supportato):

  - Fare clic con il pulsante destro del mouse sull'app iOS e scegliere **Imposta come progetto di avvio**.
  - Passare alla configurazione dell' **App Store** .
  - Usare la funzionalità di **archiviazione** per creare un archivio applicazioni.

- Infine, passare a [Xcode 6.2 +](#xcode)

  - Passare alla **finestra libreria >** e scegliere **archivi**.
  - Selezionare l'applicazione e l'archivio dall'elenco.
  - Facoltativamente **Convalida...** Archivio.
  - **Invia...** l'archivio e seguire i passaggi per caricare in iTunes Connect per la revisione e l'approvazione.

Leggere suggerimenti specifici correlati a questi elementi. Se si verificano problemi, vedere la sezione relativa alla [risoluzione dei](#troubleshooting) problemi.

<a name="provisioning" />

## <a name="distribution-provisioning-profiles"></a>Profili di provisioning della distribuzione

Per compilare per la distribuzione dell'app Store, è necessario creare un **profilo di provisioning di distribuzione** per ogni ID app nella soluzione.

Se si dispone di un ID app con caratteri jolly, *sarà necessario solo un profilo di provisioning*; Tuttavia, se si dispone di un ID app separato per ogni progetto, è necessario un profilo di provisioning per ogni ID app:

![](appstore-images/provisioningprofile-distribution-sml.png "The App Store Distribution profile")

Una volta creati tutti e tre i profili, verranno visualizzati nell'elenco. Ricordarsi di scaricare e installare ognuno di essi (facendo doppio clic su di esso):

![](appstore-images/provisioningprofiles-sml.png "The list of available profiles")

È possibile verificare il profilo di provisioning nelle **Opzioni del progetto** selezionando la schermata **Compila > firma del bundle iOS** e selezionando la configurazione **AppStore | iPhone** .

L'elenco **profilo di provisioning** mostrerà tutti i profili corrispondenti. verranno visualizzati i profili corrispondenti creati in questo elenco a discesa.

![](appstore-images/options-selectprofile-sml.png "The iOS Bundle Signing dialog")

<a name="iTunes_Connect"/>

## <a name="itunes-connect"></a>iTunes Connect

Seguire la [Panoramica della distribuzione dell'app](~/ios/deploy-test/app-distribution/index.md), in particolare:

- [Configurazione di un'app in iTunes Connect](~/ios/deploy-test/app-distribution/app-store-distribution/itunesconnect.md)
- [Pubblicazione nell'App Store](~/ios/deploy-test/app-distribution/app-store-distribution/publishing-to-the-app-store.md)

Quando si configura l'app in iTunes Connect, non dimenticare di aggiungere l'icona di espressione di controllo e le schermate:

![](appstore-images/itunesconnect-watch-sml.png "The Watch icon and screenshots in iTunes Connect")

Il file icona deve essere 1024x1024 pixel e una maschera circolare viene applicata quando viene visualizzata. L'icona non deve avere un canale alfa.

È richiesta almeno una schermata. è possibile inviare fino a cinque.
Devono essere 312x390 pixel e dimostrare l'app Watch in azione.
È possibile usare il simulatore di controllo 42 millimetri per scattare screenshot a questa dimensione.

<a name="xamarin_studio" />

## <a name="visual-studio-for-mac"></a>Visual Studio per Mac

1. Assicurarsi che l'app iOS sia il progetto di avvio. In caso contrario, fare clic con il pulsante destro del mouse per impostarlo:

   ![](appstore-images/xs-startup.png "Setting the startup project")

2. Scegliere la configurazione di compilazione **AppStore** :

   ![](appstore-images/xs-appstore.png "The AppStore build configuration")

3. Scegliere la voce di menu **compila > Archive** per avviare il processo di archiviazione:

   ![](appstore-images/xs-archive.png "The Build menu")

È anche possibile scegliere la voce di menu **visualizza > archivi...** per visualizzare gli archivi creati in precedenza.

  ![](appstore-images/xs-archives-sml.png "The Archives view")

<a name="xcode" />

## <a name="xcode"></a>Xcode

Xcode mostrerà automaticamente gli archivi creati in Visual Studio per Mac.

1. Avviare Xcode e scegliere **finestra libreria >**:

   ![](appstore-images/xc-organizer.png "The Window menu")

2. Passare alla scheda **archivi** e selezionare l'archivio creato con Visual Studio per Mac:

   ![](appstore-images/xc-archives.png "The Archives tab")

3. Facoltativamente, è possibile **convalidare...** l'archivio, quindi scegliere **Invia...** per caricare l'app in iTunes Connect.

4. Scegliere il team di sviluppo (se si appartiene a più di un) e quindi confermare l'invio:

   ![](appstore-images/xc-submit1.png "The development team section")

5. Visitare di nuovo iTunes Connect per visualizzare il file binario caricato. Passare alla pagina di configurazione dell'app e scegliere **versione provvisoria** dal menu superiore per visualizzare l'elenco delle **compilazioni** :

   [![](appstore-images/itc-prerelease-sml.png "The apps configuration page in iTunes Connect")](appstore-images/itc-prerelease.png#lightbox)

È quindi possibile inviare l'app per l'approvazione nella pagina **versioni** . Per ulteriori informazioni, vedere la [panoramica sulla distribuzione di app iOS](~/ios/deploy-test/app-distribution/index.md) .

## <a name="troubleshooting"></a>Troubleshooting

Ecco alcuni errori che possono verificarsi durante l'invio all'App Store e i passaggi da eseguire per correggerli.

### <a name="archive-menu-option-is-not-visible-in-visual-studio-for-mac"></a>L'opzione di menu Archivio non è visibile in Visual Studio per Mac

Attenersi alla [procedura](#xamarin_studio) descritta in precedenza per configurare la soluzione per l'archiviazione. Se non è possibile impostare correttamente il progetto di avvio, verificare che la configurazione di compilazione sia impostata su debug o Release prima di provare a modificare il progetto di avvio. Quindi impostare di nuovo la configurazione della build su **AppStore**.

### <a name="invalid-icon"></a>Icona Non valido

```csharp
Invalid Icon - The watch application '...watchkitextension.appex/WatchApp.app'
contains an icon file '...watchkitextension.appex/WatchApp.app/AppIcon27.5x27.5@2x.png'
with an alpha channel. Icons should not have an alpha channel.
```

Seguire le [istruzioni per rimuovere il canale alfa](~/ios/watchos/troubleshooting.md) dalle icone.

### <a name="cfbundleversion-mismatch"></a>Mancata corrispondenza di CFBundleVersion

```csharp
CFBundleVersion Mismatch. The CFBundleVersion value '1' of watch application
'...watchkitextension.appex/WatchApp.app' does not match the CFBundleVersion
value '1.0' of its containing iOS application `YouriOS.app`.
```

Tutti i progetti nella soluzione: l'app iOS, l'estensione Watch e l'app Watch devono usare lo stesso numero di versione. Modificare ogni file **info. plist** in modo che il numero di versione corrisponda esattamente a.

### <a name="missing-icons"></a>Icone mancanti

```csharp
Missing Icons. No icons found for watch application '...watchkitextension.appex/WatchApp.app'.
Please make sure that its Info.plist file includes entries for CFBundleIconFiles.
```

Seguire le istruzioni riportate nelle [Icone uso](~/ios/watchos/app-fundamentals/icons.md) per aggiungere tutte le immagini necessarie al progetto Watch app.

### <a name="missing-icon"></a>Icona mancante

```csharp
Missing Icon. The watch application '...watchkitextension.appex/WatchApp.app'
is missing icon with name pattern '*44x44@2x.png' (Home Screen 42mm).
```

Assicurarsi di disporre della versione più recente di Visual Studio per Mac e che **AppIcon. appiconset** contenga un set completo di immagini. Se questo errore viene ancora visualizzato, visualizzare l'origine del file **Contents. JSON** per confermare che contiene una voce per tutte le immagini obbligatorie. In alternativa, dopo aver verificato che si sta usando la versione più recente di Novell, eliminare e ricreare **AppIcon. appiconset**.

> [!IMPORTANT]
> È presente un bug noto nel supporto dell'icona del controllo Visual Studio per Mac: prevede un'immagine di pixel 88x88 per l'immagine **29x29@3x** (che deve essere 87x87 pixel).

Non è possibile risolvere questo problema in Visual Studio per Mac: modificare l'asset di immagine in Xcode o modificare manualmente il file **Contents. JSON** (in modo che corrisponda a [questo esempio](https://github.com/xamarin/monotouch-samples/blob/master/WatchKit/WatchKitCatalog/WatchApp/Resources/Images.xcassets/AppIcons.appiconset/Contents.json#L126-L132)).

### <a name="invalid-watchkit-support"></a>Supporto WatchKit non valido

```csharp
Invalid WatchKit Support - The bundle contains an invalid implementation of WatchKit.
The app may have been built or signed with non-compliant or pre-release tools.
```

Questo messaggio può essere visualizzato durante la convalida e l'invio oppure in un messaggio di posta elettronica automatizzato da iTunes Connect dopo un caricamento apparentemente riuscito.
<!--
Ensure you are using the latest version of Xcode and Xamarin's tools.
-->
> [!IMPORTANT]
> È necessario **archiviare** l'app in Visual Studio per Mac, quindi passare a Xcode 6.2 + per convalidare e caricare in iTunes Connect.

Usare il canale Novell stabile e Xcode 6.2 +.

### <a name="invalid-provisioning-profile"></a>Profilo di provisioning non valido

```csharp
Invalid Provisioning Profile. The provisioning profile included in the bundle
...iOSWatchApp.watchkitapp [iOSWatchApp.app/PlugIns/...iOSWatchApp.watchkitextension.appex/WatchApp.app]
is invalid. [Missing code-signing certificate.]
```

I **profili di provisioning della distribuzione** devono essere forniti per tutti e tre i progetti in una soluzione di app di controllo: l'app iOS, l'estensione Watch e l'app Watch, in modo esplicito (tre profili) o tramite un singolo profilo jolly. Verificare che i profili di provisioning esistano in iOS Dev Center e che siano stati scaricati e aggiunti al Mac.

### <a name="invalid-code-signing-entitlements"></a>Diritti di firma del codice non validi

```csharp
ITMS-90046: Invalid Code Signing Entitlements. Your application bundle's signature contains
code signing entitlements that are not supported on iOS. Specifically, value
'...watchkitextension' for key 'application-identifier' in '...watchkitextension'
is not supported. The value should be a string startign with your TEAMID, followed
by a dot '.' followed by the bundle identifier.
```

Verificare che i profili di provisioning siano impostati correttamente in Apple Dev Center e che siano stati scaricati e installati. Verificare anche che siano impostati nella finestra proprietà di Visual Studio per Mac per ogni progetto.

### <a name="invalid-architecture"></a>Architettura non valida

```csharp
Invalid architecture: Apps that include an app extension
and framework must support arm64.
```

È possibile aggiungere solo app Watch [API unificata (64 bit)](~/cross-platform/macios/unified/index.md) Xamarin.iOS.
Fare clic con il pulsante destro del mouse sul progetto di app iOS, quindi scegliere **opzioni > compilare > iOS compila > scheda Avanzate** e assicurarsi che le **architetture supportate** per la configurazione AppStore-iPhone includano **arm64** (ad esempio, **ARMv7 + arm64**).

### <a name="this-bundle-is-invalid"></a>Questo bundle non è valido.

```csharp
ITMS-90068: This bundle is invalid. The value provided for the key
MinimumOSVersion '8.3' is not acceptable.
```

L'applicazione iOS padre deve avere MinimumOSVersion impostato su "8,2" o versioni precedenti.

### <a name="non-public-api-usage"></a>Utilizzo API non pubblico

```csharp
Your app contains non-public API usage.
Please review the errors, and resubmit your application.
```

Assicurarsi di usare la versione più recente degli strumenti di Xcode e Novell.
Il codice non deve accedere ad alcuna API non pubblica.

### <a name="build-error-mt5309"></a>Errore di compilazione MT5309

```csharp
Error MT5309: Native linking error: clang: error: no such file or directory:
```

Questo errore è probabilmente dovuto al fatto che l'installazione di Xcode è stata rinominata da **Xcode. app**. Ad esempio, questo errore si verificherà se si rinomina l'installazione in **Xcode 6.2. app**.

## <a name="related-links"></a>Collegamenti correlati

- [Guida all'invio di Apple WatchKit](https://developer.apple.com/app-store/watch/)
