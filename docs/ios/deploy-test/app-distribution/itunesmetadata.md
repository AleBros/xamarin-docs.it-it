---
title: File iTunesMetadata.plist nelle app Xamarin.iOS
description: Questo articolo illustra il file iTunesMetadata.plist usato per fornire informazioni a iTunes su un'applicazione iOS con la distribuzione ad hoc per il test o la distribuzione Enterprise.
ms.prod: xamarin
ms.assetid: 70676eba-6a99-4a3a-bccc-84359fe9c2c3
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/19/2017
ms.openlocfilehash: af1ad32e605bbbe899ec8596a60cdabf226372db
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2018
ms.locfileid: "50111304"
---
# <a name="the-itunesmetadataplist-file-in-xamarinios-apps"></a>File iTunesMetadata.plist nelle app Xamarin.iOS

_Questo articolo illustra il file iTunesMetadata.plist usato per fornire informazioni a iTunes su un'applicazione iOS con la distribuzione ad hoc per il test o la distribuzione a livello aziendale._

Quando un'applicazione iOS viene creata in iTunes Connect (per la vendita o il rilascio gratuito dall'App Store iTunes), lo sviluppatore può specificare informazioni come il genere dell'applicazione, il genere secondario, le informazioni sul copyright, i dispositivi iOS supportati e le funzionalità dei dispositivi necessarie. Per le applicazioni iOS distribuite ai tester o agli utenti Enterprise tramite la distribuzione ad hoc, queste informazioni mancano.

Per fornire le informazioni mancanti a una distribuzione ad hoc, è possibile creare un file `iTunesMetadata.plist` facoltativo e includerlo nel file IPA delle applicazioni. Questo file con estensione plist è un file XML con formattazione speciale, contenente coppie chiave/valore che definiscono le informazioni su una determinata applicazione iOS. Per altre informazioni, vedere [Property List Programming Guide](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/PropertyLists/Introduction/Introduction.html) (Guida alla programmazione di elenchi di proprietà) di Apple.

<a name="iTunesMetadata_contents" />

## <a name="the-itunesmetadataplist-contents"></a>Contenuto di iTunesMetadata.plist

Il seguente è un esempio di un file `iTunesMetadata.plist` tipico usato per definire le informazioni di iTunes per una distribuzione ad hoc:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
    <key>UIRequiredDeviceCapabilities</key>
    <dict>
        <key>armv7</key>
        <true/>
        <key>front-facing-camera</key>
        <true/>
    </dict>
    <key>artistName</key>
    <string>Company, Inc.</string>
    <key>bundleDisplayName</key>
    <string>App Name</string>
    <key>bundleShortVersionString</key>
    <string>1.5.1</string>
    <key>bundleVersion</key>
    <string>1.5.1</string>
    <key>copyright</key>
    <string>© 2015 Company, Inc.</string>
    <key>drmVersionNumber</key>
    <integer>0</integer>
    <key>fileExtension</key>
    <string>.app</string>
    <key>gameCenterEnabled</key>
    <false/>
    <key>gameCenterEverEnabled</key>
    <false/>
    <key>genre</key>
    <string>Games</string>
    <key>genreId</key>
    <integer>6014</integer>
    <key>itemName</key>
    <string>App Name</string>
    <key>kind</key>
    <string>software</string>
    <key>playlistArtistName</key>
    <string>Company, Inc.</string>
    <key>playlistName</key>
    <string>App Name</string>
    <key>releaseDate</key>
    <string>2015-11-18T03:23:10Z</string>
    <key>s</key>
    <integer>143441</integer>
    <key>softwareIconNeedsShine</key>
    <false/>
    <key>softwareSupportedDeviceIds</key>
    <array>
        <integer>9</integer>
    </array>
    <key>softwareVersionBundleId</key>
    <string>com.company.appid</string>
    <key>subgenres</key>
    <array>
        <dict>
            <key>genre</key>
            <string>Puzzle</string>
            <key>genreId</key>
            <integer>7012</integer>
        </dict>
        <dict>
            <key>genre</key>
            <string>Word</string>
            <key>genreId</key>
            <integer>7019</integer>
        </dict>
    </array>
    <key>versionRestrictions</key>
    <integer>16843008</integer>
</dict>
</plist>

```

I valori delle singole chiavi sono illustrati sotto in dettaglio.

### <a name="uirequireddevicecapabilities"></a>UIRequiredDeviceCapabilities

La chiave `UIRequiredDeviceCapabilities` consente a iTunes di conoscere le funzionalità specifiche di un dispositivo iOS, necessarie per installare un'applicazione iOS in tale dispositivo. Viene fornita come dizionario (`<dict>...</dict>`) di funzionalità (`<key>...</key>`) a ognuna delle quali corrisponde un valore booleano. Se il valore di una funzionalità è `true`, tale funzionalità deve essere presente. Se è `false`, la funzionalità non deve essere presente nel dispositivo. Ad esempio:

```xml
<key>UIRequiredDeviceCapabilities</key>
<dict>
    <key>armv7</key>
    <true/>
    <key>front-facing-camera</key>
    <true/>
</dict>
```
Specifica che il dispositivo iOS deve supportare il set di istruzioni ARM7 e avere una fotocamera rivolta in avanti prima che questa applicazione possa essere installata nel dispositivo. Per un elenco completo di valori consentiti, vedere la documentazione su [UIRequiredDeviceCapabilities](https://developer.apple.com/library/ios/documentation/General/Reference/InfoPlistKeyReference/Articles/iPhoneOSKeys.html#//apple_ref/doc/uid/TP40009252-SW3) di Apple.

### <a name="artistname-and-playlistartistname"></a>artistName e playlistArtistName

Usare le chiavi `artistName` e `playlistArtistName` per definire il nome della società che ha creato l'applicazione iOS che verrà visualizzato in iTunes. Esempio:

```xml
<key>artistName</key>
<string>Company, Inc.</string>
...
<key>playlistArtistName</key>
<string>Company, Inc.</string>
```

### <a name="bundledisplayname-itemname-and-playlistname"></a>bundleDisplayName, itemName e playlistName

Usare le chiavi `bundleDisplayName`, `itemName` e `playlistName` per definire il nome dell'applicazione iOS che verrà visualizzato in iTunes. Esempio:

```xml
<key>bundleDisplayName</key>
<string>App Name</string>
...
<key>itemName</key>
<string>App Name</string>
...
<key>playlistName</key>
<string>App Name</string>
```

### <a name="bundleshortversionstring-and-bundleversion"></a>bundleShortVersionString e bundleVersion

Usare le chiavi `bundleShortVersionString` e `bundleVersion` per definire il numero di versione dell'applicazione iOS che verrà visualizzato in iTunes. Esempio:

```xml
<key>bundleShortVersionString</key>
<string>1.5.1</string>
<key>bundleVersion</key>
<string>1.5.1</string>
```

### <a name="softwareversionbundleid"></a>softwareVersionBundleId

Usare la chiave `softwareVersionBundleId` per specificare l'ID del bundle per l'applicazione iOS. Esempio:

```xml
<key>softwareVersionBundleId</key>
<string>com.company.appid</string>
```

### <a name="copyright"></a>copyright

Usare la chiave `copyright` per definire le informazioni sul copyright visualizzate in iTunes. Esempio:

```xml
<key>copyright</key>
<string>© 2015 Company, Inc.</string>
```

### <a name="releasedate"></a>releaseDate

Usare la chiave `releaseDate` per specificare una Data di rilascio per l'applicazione iOS che verrà visualizzata in iTunes. Esempio:

```xml
<key>releaseDate</key>
<string>2015-11-18T03:23:10Z</string>
```

### <a name="softwareiconneedsshine"></a>softwareIconNeedsShine

Usare la chiave `softwareIconNeedsShine` per comunicare a iTunes se l'icona dell'applicazione iOS richiede un _punto luce_ per iOS 6 (e versioni precedenti). Esempio:

```xml
<key>softwareIconNeedsShine</key>
<false/>
```

### <a name="gamecenterenabled-and-gamecentereverenabled"></a>gameCenterEnabled e gameCenterEverEnabled

Usare le chiavi `gameCenterEnabled` e `gameCenterEverEnabled` per comunicare a iTunes se questa applicazione iOS supporta Game Center di Apple. Esempio:

```xml
<key>gameCenterEnabled</key>
<false/>
<key>gameCenterEverEnabled</key>
<false/>
```

### <a name="genre-genreid-and-subgenres"></a>genre, genreId e subgenres

Usare le chiavi `genre` e `genreId` per comunicare a iTunes il genere a cui appartiene l'applicazione iOS. Esempio:

```xml
<key>genre</key>
<string>Games</string>
<key>genreId</key>
<integer>6014</integer>
```

La chiave `subgenres` può facoltativamente essere usata per definire fino a due generi secondari per l'applicazione iOS. Esempio:

```xml
<key>subgenres</key>
<array>
    <dict>
        <key>genre</key>
        <string>Puzzle</string>
        <key>genreId</key>
        <integer>7012</integer>
    </dict>
    <dict>
        <key>genre</key>
        <string>Word</string>
        <key>genreId</key>
        <integer>7019</integer>
    </dict>
</array>
```

Per le applicazioni iOS, Apple definisce attualmente i generi e gli ID di genere seguenti:

[!include[](~/ios/includes/table-appstore.md)]

Per altre informazioni vedere la documentazione [Genre IDs Appendix](http://www.apple.com/itunes/affiliates/resources/documentation/genre-mapping.html) (Appendice sugli ID di genere) di Apple.

### <a name="softwaresupporteddeviceids"></a>softwareSupportedDeviceIds

Usare la chiave `softwareSupportedDeviceIds` per comunicare a iTunes i dispositivi iOS supportati da questa applicazione iOS. Esempio:

```xml
<key>softwareSupportedDeviceIds</key>
<array>
    <integer>9</integer>
</array>
```

Dove sono disponibili i valori seguenti:

- 1: iPhone classici
- 2: iPod Touch
- 4: iPad
- 9: iPhone moderni

### <a name="standard-keys"></a>Chiavi standard

Le chiavi seguenti sono incluse in tutti i file `iTunesMetadata.plist` per le applicazioni iOS e hanno sempre gli stessi valori:

```xml
<key>drmVersionNumber</key>
<integer>0</integer>
<key>fileExtension</key>
<string>.app</string>
...
<key>kind</key>
<string>software</string>
...
<key>s</key>
<integer>143441</integer>
...
<key>versionRestrictions</key>
<integer>16843008</integer>
```

<a name="iTunesMetadata_creating" />

## <a name="creating-an-itunesmetadataplist-file"></a>Creazione di un file iTunesMetadata.plist

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

 Quando si usa un file `iTunesMetadata.plist` in Visual Studio per Mac, sono disponibili due opzioni:

- Creare e gestire il file usando l'editor di file plist visivo di Visual Studio per Mac.
- Creare e gestire il file in un editor di testo normale.

 Entrambe le opzioni sono illustrate sotto in dettaglio.

### <a name="using-the-visual-plist-editor"></a>Uso dell'editor di file plist visivo

Seguire questa procedura:

1. In **Esplora soluzioni** fare clic con il pulsante destro del mouse sul file di progetto Xamarin.iOS e scegliere **Aggiungi** > **Nuovo file**.
2. Nella finestra di dialogo Nuovo file selezionare **iOS** > **Elenco proprietà**:

    ![](itunesmetadata-images/image01.png "Selezionare iOS, Elenco proprietà")
3. Immettere `iTunesMetadata` in **Nome** e fare clic sul pulsante **Nuovo**.
4. Fare doppio clic sul file `iTunesMetadata.plist` in **Esplora soluzioni** per aprirlo e modificarlo:

    ![](itunesmetadata-images/image02.png "Editor di iTunesMetadata.plist")
5. Fare clic sul segno **+** verde per creare una nuova voce e immettere `UIRequiredDeviceCapabilities` come nome della chiave:

    ![](itunesmetadata-images/image03.png "Creare una nuova voce e immettere UIRequiredDeviceCapabilities come nome della chiave")
6. Fare clic sul tipo valore **Stringa** e selezionare **Dizionario** nell'elenco popup:

    ![](itunesmetadata-images/image04.png "Selezionare Dizionario nell'elenco popup")
7. Fare clic sulla freccia verso il basso a sinistra del nome della proprietà per visualizzare le voci del dizionario:

    ![](itunesmetadata-images/image05.png "Visualizzare le voci del dizionario")
8. Fare clic sul testo **Aggiungi una nuova voce**, quindi fare clic sul segno **+** verde per aggiungere una voce al dizionario:

    ![](itunesmetadata-images/image06.png "Aggiungere una voce al dizionario")
9. Immettere `armv7` come nome della chiave, selezionare un tipo **Booleano** e immettere **Sì** come valore:

    ![](itunesmetadata-images/image07.png "Immettere armv7 come nome della chiave, selezionare un tipo Booleano e immettere Sì come valore")
10. Ripetere i passaggi precedenti fino a riempire il file `iTunesMetadata.plist` con tutte le coppie chiave/valore necessarie. Per altre informazioni dettagliate, vedere la sezione [Contenuto di iTunesMetadata.plist](#iTunesMetadata_contents) sopra.

11. Salvare le modifiche apportate al file con estensione plist.

### <a name="using-a-plain-text-editor"></a>Uso di un editor di testo normale

Seguire questa procedura:

1. In un editor di testo normale creare un nuovo file di testo e denominarlo `iTunesMetadata.plist`.
2. Copiare il contenuto di esempio dalla sezione [Contenuto di iTunesMetadata.plist](#iTunesMetadata_contents) sopra.
3. Incollare il contenuto nel file e modificarlo in base alla necessità.
4. Salvare il file e tornare a Visual Studio per Mac.
5. In **Esplora soluzioni** fare clic con il pulsante destro del mouse sul file di progetto Xamarin.iOS e scegliere **Aggiungi** > **File esistenti**.
6. Nella finestra di dialogo Apri file selezionare il file `iTunesMetadata.plist` creato sopra e fare clic sul pulsante **OK**.
7. Lasciare l'opzione **Azione di compilazione** di questo file impostata su **Nessuna**.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Il plug-in Xamarin per Visual Studio supporta solo un editor visivo per i file `Info.plist` e `Entitlement.plist`, quindi è necessario creare il file `iTunesMetadata.plist` in un editor di testo standard e includerlo manualmente nel progetto Xamarin.iOS.

Seguire questa procedura:

1. In un editor di testo normale creare un nuovo file di testo e denominarlo `iTunesMetadata.plist`.
2. Copiare il contenuto di esempio dalla sezione [Contenuto di iTunesMetadata.plist](#iTunesMetadata_contents) sopra.
3. Incollare il contenuto nel file e modificarlo in base alla necessità.
4. Salvare il file e tornare a Visual Studio.
5. In **Esplora soluzioni** fare clic con il pulsante destro del mouse sul file di progetto Xamarin.iOS e scegliere **Aggiungi** > **File esistenti**.
6. Nella finestra di dialogo Apri file selezionare il file `iTunesMetadata.plist` creato sopra e fare clic sul pulsante **Apri**.
7. Lasciare l'opzione **Azione di compilazione** di questo file impostata su **Nessuna**.

-----

Selezionare in seguito questo file `iTunesMetadata.plist` durante la preparazione della compilazione del file IPA nell'IDE.

## <a name="summary"></a>Riepilogo

Questo articolo ha illustrato il file `iTunesMetadata.plist` che può essere usato per comunicare a iTunes informazioni su un'applicazione iOS distribuita con la modalità ad hoc. Ha descritto la chiave standard nel file con estensione plist e come creare e gestire il file in Visual Studio e Visual Studio per Mac.

## <a name="related-links"></a>Collegamenti correlati

- [Distribuzione tramite l'App Store](~/ios/deploy-test/app-distribution/app-store-distribution/index.md)
- [Configurazione di un'app in iTunes Connect](~/ios/deploy-test/app-distribution/app-store-distribution/itunesconnect.md)
- [Pubblicazione nell'App Store](~/ios/deploy-test/app-distribution/app-store-distribution/publishing-to-the-app-store.md)
- [Distribuzione interna](~/ios/deploy-test/app-distribution/in-house-distribution.md)
- [Distribuzione ad hoc](~/ios/deploy-test/app-distribution/ad-hoc-distribution.md)
- [Supporto IPA](~/ios/deploy-test/app-distribution/ipa-support.md)
- [Risoluzione dei problemi](~/ios/deploy-test/troubleshooting.md)
