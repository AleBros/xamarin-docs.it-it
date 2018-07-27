---
title: Distribuzione di App watchOS per l'App Store
description: Questo documento descrive come distribuire le app watchOS compilate con Xamarin per l'App Store. Esamina i profili di provisioning di distribuzione e iTunes Connect e vengono inoltre forniti alcuni suggerimenti sulla risoluzione dei problemi.
ms.prod: xamarin
ms.assetid: DBE16040-70D2-4F61-B5F3-C8D213DBC754
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/17/2017
ms.openlocfilehash: 90058f5074759fdded5d259004cb40c0cb7ea212
ms.sourcegitcommit: ffb0f3dbf77b5f244b195618316bbd8964541e42
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/26/2018
ms.locfileid: "39276067"
---
# <a name="deploying-watchos-apps-to-the-app-store"></a>Distribuzione di App watchOS per l'App Store

> [!IMPORTANT]
> Assicurarsi di rivedere [Watch Kit invio Guide di Apple](https://developer.apple.com/app-store/watch/)e vedere il [risoluzione dei problemi](#Troubleshooting) sezione per qualsiasi tipo di problema.

- Assicurarsi di che avere:
  - [**I profili di Provisioning di distribuzione** ](#provisioning) creato per i progetti.
  - Il **destinazione di distribuzione** (`MinimumOSVersion`) per iOS padre è impostata su app **8.2** o versioni precedenti (8.3 non è supportata).

- Nelle [ **iTunes Connect**](#iTunes_Connect):

  - Creare iOS per la voce dell'app (o aggiungere un **nuova versione** a un'app esistente).
  - Aggiungere schermate e l'icona di espressioni di controllo.

- Quindi nella [Visual Studio per Mac](#xamarin_studio) (Visual Studio non è attualmente supportato):

  - Fare doppio clic su app per iOS e scegliere **imposta come progetto di avvio**.
  - Modificare il **App Store** configurazione.
  - Usare la **Archive** funzionalità creare un archivio di applicazione.

- Infine, passare a [Xcode 6.2 +](#xcode)

  - Andare alla **finestra > Organizer** e scegliere **archivi**.
  - Selezionare l'applicazione e l'archivio nell'elenco.
  - (Facoltativo) **Convalida...**  dell'archivio.
  - **Invia...**  di archivio e seguire i passaggi per caricare in iTunes connettono per la revisione e approvazione.

Leggi i suggerimenti specifici correlati a questi elementi seguenti. Vedere le [Troubleshooting](#Troubleshooting) sezione in caso di problemi.

<a name="provisioning" />

## <a name="distribution-provisioning-profiles"></a>Distribuzione di profili di Provisioning

Per compilare per la distribuzione di App Store è necessario creare un **profilo di Provisioning di distribuzione** per ogni ID App nella soluzione.

Se si dispone di un ID App, con caratteri jolly *sarà necessario un solo profilo di Provisioning*; ma se si ha un ID App separate per ogni progetto, è necessario un profilo di provisioning per ogni ID App:

![](appstore-images/provisioningprofile-distribution-sml.png "Il profilo di distribuzione di App Store")

Dopo aver creato tutte e tre i profili, questi verranno visualizzati nell'elenco. Ricordarsi di scaricare e installare ciascuno di essi (facendo doppio clic su di esso):

![](appstore-images/provisioningprofiles-sml.png "L'elenco dei profili disponibili")

È possibile verificare il profilo di provisioning nel **opzioni progetto** selezionando il **compilazione > firma del Bundle iOS** dello schermo e selezionare il **dall'App Store | iPhone** configurazione.

Il **profilo di Provisioning** elenco verranno visualizzati tutti i profili corrispondenti, si dovrebbero vedere i profili corrispondenti che sono stati creati in questo elenco a discesa.

![](appstore-images/options-selectprofile-sml.png "La finestra di dialogo firma del Bundle iOS")

<a name="iTunes_Connect"/>

## <a name="itunes-connect"></a>iTunes Connect

Seguire le [Cenni preliminari sulla distribuzione di app](~/ios/deploy-test/app-distribution/index.md), in particolare:

- [Configurazione di un'app in iTunes Connect](~/ios/deploy-test/app-distribution/app-store-distribution/itunesconnect.md)
- [Pubblicazione nell'App Store](~/ios/deploy-test/app-distribution/app-store-distribution/publishing-to-the-app-store.md)

Quando si configura l'app in iTunes Connect, non dimenticare di aggiungere l'icona di espressioni di controllo e gli screenshot:

![](appstore-images/itunesconnect-watch-sml.png "L'icona di espressioni di controllo e gli screenshot in iTunes Connect")

Il file di icona deve essere di 1024 x 1024 pixel e disporrà di una maschera circolare applicata quando viene visualizzato. L'icona non deve avere un canale alfa.

Almeno una schermata è obbligatorio, fino a cinque può essere inviato.
Devono essere 312 x 390 pixel e dimostrare l'App Watch in azione.
È possibile usare il simulatore di espressioni di controllo 42mm per acquisire screenshot in questa dimensione.


<a name="xamarin_studio" />

## <a name="visual-studio-for-mac"></a>Visual Studio per Mac

1. Assicurarsi che l'app per iOS è il progetto di avvio. In caso contrario, fare doppio clic per impostare le ore:

  ![](appstore-images/xs-startup.png "Impostazione del progetto di avvio")

2. Scegliere il **dall'App Store** configurazione compilazione:

  ![](appstore-images/xs-appstore.png "La configurazione della build dall'App Store")

3. Scegliere il **compilazione > archivio** voce di menu per avviare il processo di archiviazione:

  ![](appstore-images/xs-archive.png "Dal menu Compila")

È anche possibile scegliere di **visualizzazione > archivi...**  voce di menu per visualizzare gli archivi che sono stati creati in precedenza.

  ![](appstore-images/xs-archives-sml.png "La visualizzazione archivi")

<a name="xcode" />

## <a name="xcode"></a>Xcode

Xcode visualizzerà automaticamente gli archivi creati in Visual Studio per Mac.

1. Avviare Xcode e scegliere **finestra > organizzatore**:

  ![](appstore-images/xc-organizer.png "Menu finestra")

2. Passare al **archivi** scheda e selezionare l'archivio che è stato creato con Visual Studio per Mac:

  ![](appstore-images/xc-archives.png "Nella scheda archivi")

3. Facoltativamente **convalida...**  archivio, quindi scegliere **Submit...**  per caricare l'app a iTunes Connect.

4. Scegliere il team di sviluppo (se si appartiene a più di uno) e quindi confermare l'invio:

  ![](appstore-images/xc-submit1.png "La sezione del team di sviluppo")

5. Visitare iTunes Connect nuovamente per visualizzare il file binario caricato. Passare alla pagina di configurazione dell'app e scegli **Prerelease** dal menu in alto per visualizzare i **Compila** elenco:

  [![](appstore-images/itc-prerelease-sml.png "La pagina di configurazione delle App in iTunes Connect")](appstore-images/itc-prerelease.png#lightbox)

È quindi possibile inviare l'app per l'approvazione nel **versioni** pagina. Vedere le [Cenni preliminari sulla distribuzione di app iOS](~/ios/deploy-test/app-distribution/index.md) per altre informazioni.


## <a name="troubleshooting"></a>Risoluzione dei problemi

Ecco alcuni errori che possono verificarsi durante l'invio per l'App Store e i passaggi da eseguire per correggerli.

### <a name="archive-menu-option-is-not-visible-in-visual-studio-for-mac"></a>Opzione di menu di archivio non è visibile in Visual Studio per Mac

Seguire le [passaggi precedenti](#xamarin_studio) per configurare la soluzione per l'archiviazione. Se è possibile impostare il progetto di avvio in modo corretto, verificare che la configurazione di compilazione prima di tutto è impostata su Debug o rilascio prima di provare a modificare il progetto di avvio. Quindi impostare nuovamente la configurazione della build per **dall'App Store**.

### <a name="invalid-icon"></a>Icona Non valido

```csharp
Invalid Icon - The watch application '...watchkitextension.appex/WatchApp.app'
contains an icon file '...watchkitextension.appex/WatchApp.app/AppIcon27.5x27.5@2x.png'
with an alpha channel. Icons should not have an alpha channel.
```

Seguire le [istruzioni per rimuovere il canale alfa](~/ios/watchos/troubleshooting.md) tra le icone.

### <a name="cfbundleversion-mismatch"></a>Mancata corrispondenza del valore CFBundleVersion

```csharp
CFBundleVersion Mismatch. The CFBundleVersion value '1' of watch application
'...watchkitextension.appex/WatchApp.app' does not match the CFBundleVersion
value '1.0' of its containing iOS application `YouriOS.app`.
```

Tutti i progetti nella soluzione - App iOS, l'estensione di espressioni di controllo e l'App Watch - devono usare lo stesso numero di versione. Modificare ognuno **Info. plist** file in modo che il numero di versione corrisponda esattamente.

### <a name="missing-icons"></a>Icone mancanti

```csharp
Missing Icons. No icons found for watch application '...watchkitextension.appex/WatchApp.app'.
Please make sure that its Info.plist file includes entries for CFBundleIconFiles.
```

Seguire le istruzioni di [funziona con icone](~/ios/watchos/app-fundamentals/icons.md) per aggiungere tutte le immagini necessarie per il progetto di App Watch.

### <a name="missing-icon"></a>Icona manca

```csharp
Missing Icon. The watch application '...watchkitextension.appex/WatchApp.app'
is missing icon with name pattern '*44x44@2x.png' (Home Screen 42mm).
```

Assicurarsi di aver la versione più recente di Visual Studio per Mac e che il **AppIcon.appiconset** contiene un set completo di immagini. Se viene comunque visualizzato questo errore, visualizzare l'origine del **Contents.json** per confermare che contenga una voce per tutte le immagini necessarie. In alternativa, dopo che è stato verificato che si usa la versione più recente di Xamarin, eliminare e ricreare il **AppIcon.appiconset**.

> [!IMPORTANT]
> C'è un bug noto in Visual Studio per il supporto di icona di espressioni di controllo del Mac: è previsto che un'immagine di 88 x 88 pixel per il **29x29@3x** immagine (che deve essere 87 x 87 pixel).


Non è possibile risolvere questo problema in Visual Studio per Mac - modificare l'asset di immagine in Xcode o modificare manualmente il **Contents.json** file (in modo che corrispondano [in questo esempio](https://github.com/xamarin/monotouch-samples/blob/master/WatchKit/WatchKitCatalog/WatchApp/Resources/Images.xcassets/AppIcons.appiconset/Contents.json#L126-L132)).



### <a name="invalid-watchkit-support"></a>Supporto WatchKit non validi

```csharp
Invalid WatchKit Support - The bundle contains an invalid implementation of WatchKit.
The app may have been built or signed with non-compliant or pre-release tools.
```

Questo messaggio può essere visualizzato durante la convalida e l'invio o tramite posta elettronica automatizzati da iTunes Connect dopo un'operazione di caricamento apparentemente esito positivo.
<!--
Ensure you are using the latest version of Xcode and Xamarin's tools.
-->
> [!IMPORTANT]
> È necessario **Archive** l'app in Visual Studio per Mac e quindi passare a Xcode 6.2 e per convalidare e caricare in iTunes Connect.


Usare il canale stabile Xamarin e Xcode 6.2 +.



### <a name="invalid-provisioning-profile"></a>Profilo di Provisioning non è valido

```csharp
Invalid Provisioning Profile. The provisioning profile included in the bundle
...iOSWatchApp.watchkitapp [iOSWatchApp.app/PlugIns/...iOSWatchApp.watchkitextension.appex/WatchApp.app]
is invalid. [Missing code-signing certificate.]
```

**I profili di Provisioning di distribuzione** deve essere fornito per tutti i tre progetti in una soluzione di app watch: l'App iOS, l'estensione di espressioni di controllo e l'App Watch - un in modo esplicito (tre profili) o tramite un profilo singolo carattere jolly. Verificare che i profili di provisioning presenti nel centro per sviluppatori iOS e di avere scaricato e aggiunte al Mac.

### <a name="invalid-code-signing-entitlements"></a>I diritti di firma del codice non valido

```csharp
ITMS-90046: Invalid Code Signing Entitlements. Your application bundle's signature contains
code signing entitlements that are not supported on iOS. Specifically, value
'...watchkitextension' for key 'application-identifier' in '...watchkitextension'
is not supported. The value should be a string startign with your TEAMID, followed
by a dot '.' followed by the bundle identifier.
```

Verificare che i profili di provisioning sono configurazione correttamente nel centro per sviluppatori di Apple e aver scaricato e installato li. Verificare anche che siano impostate in Visual Studio per la finestra delle proprietà del Mac per ogni progetto.

### <a name="invalid-architecture"></a>Architettura non è valido

```csharp
Invalid architecture: Apps that include an app extension
and framework must support arm64.
```

È possibile aggiungere solo le app Watch [API unificata (64 bit)](~/cross-platform/macios/unified/index.md) App xamarin. IOS.
Pulsante destro del mouse sul progetto di app iOS, quindi andare al **Opzioni > compilazione > compilazione iOS > scheda Avanzate** e assicurarsi che il **architetture supportate** per iPhone dall'App Store-configurazione include **ARM64** (ad es. **ARMv7 + ARM64**).

### <a name="this-bundle-is-invalid"></a>Questo pacchetto non è valido.

```csharp
ITMS-90068: This bundle is invalid. The value provided for the key
MinimumOSVersion '8.3' is not acceptable.
```

L'applicazione iOS padre deve avere il MinimumOSVersion impostato su '8.2' o versioni precedenti.

### <a name="non-public-api-usage"></a>Utilizzo API non pubblici

```csharp
Your app contains non-public API usage.
Please review the errors, and resubmit your application.
```

Assicurarsi di usare la versione più recente di strumenti Xcode e Xamarin.
Il codice non deve accedere tutte le API non pubblici.

### <a name="build-error-mt5309"></a>Compilare MT5309 errore

```csharp
Error MT5309: Native linking error: clang: error: no such file or directory:
```

Questo errore è probabilmente il risultato della necessità di rinominare l'installazione di Xcode dalla **Xcode.app**. Ad esempio, questo errore si verificherà se si rinomina l'installazione possa **XCode 6.2.app**.



## <a name="related-links"></a>Collegamenti correlati

- [Guida di Apple WatchKit invio](https://developer.apple.com/app-store/watch/)
