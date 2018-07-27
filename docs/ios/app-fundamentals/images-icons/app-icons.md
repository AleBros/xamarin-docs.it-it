---
title: Icone dell'applicazione xamarin. IOS
description: "Questo documento descrive come usare i vari icone dell'applicazione xamarin. ios: l'icona dell'applicazione stessa, icone in evidenza, le icone per impostazioni e illustrazione di iTunes."
ms.prod: xamarin
ms.assetid: B7791574-4A0F-4CB6-8C18-36D40B5C91EB
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 05/22/2017
ms.openlocfilehash: cd67c564461721ade6f3eb269b461ddea5e2d2c4
ms.sourcegitcommit: ffb0f3dbf77b5f244b195618316bbd8964541e42
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/26/2018
ms.locfileid: "39276002"
---
# <a name="application-icons-in-xamarinios"></a>Icone dell'applicazione xamarin. IOS

Gli argomenti seguenti vengono descritti nel dettaglio:

* [Applicazione, Spotlight e impostazioni icone](#icon-types) -diversi tipi di icone necessarie per un'app per iOS.
* [La gestione delle icone con cataloghi Asset](#managing) : la gestione delle icone dell'applicazione tramite cataloghi Asset.
* [Illustrazione di iTunes](#itunes) -fornendo l'illustrazione di iTunes obbligatorio per il metodo Ad-Hoc di recapito all'applicazione.

<a name="icon-types" />

## <a name="application-spotlight-and-settings-icons"></a>Applicazione, Spotlight e impostazioni icone

Nello stesso modo che un'app xamarin. IOS è possibile usare gli asset delle immagini per i controlli dell'interfaccia utente e come icone documento, gli asset delle immagini è utilizzabile per fornire le icone dell'applicazione. Gli screenshot seguenti da un iPad illustra tre usi delle icone in iOS:

- **Icona dell'applicazione** -tutte le app iOS è necessario definire un'icona dell'applicazione. Questa è l'icona che l'utente può toccare la schermata iniziale iOS per avviare l'app. Inoltre, questa icona viene usata dalla Game Center, se applicabile. Esempio: 

    [![](app-icons-images/000.png "Icona dell'applicazione")](app-icons-images/000-full.png#lightbox)
- **Spotlight icona** : ogni volta che l'utente immette il nome di un'app in una ricerca di Spotlight, questa icona viene visualizzata. Esempio: 

    [![](app-icons-images/000a.png "Icona in primo piano")](app-icons-images/000a-full.png#lightbox)
- **Icona delle impostazioni** : se l'utente immette il **delle impostazioni** app nel dispositivo iOS, questa icona verrà visualizzata alla fine del **impostazioni** elenco per l'app. Esempio: 

    [![](app-icons-images/000b.png "Icona delle impostazioni")](app-icons-images/000b-full.png#lightbox)

Le dimensioni di asset di immagini e le risoluzioni seguenti saranno necessarie per supportare tutti i tipi di icona necessari da un'app xamarin. IOS destinate a iOS 5 tramite iOS 9 (o versioni successive):

### <a name="iphone-icon-sizes"></a>Dimensioni delle icone iPhone

- **iPhone: iOS 9 e 10 (iPhone 6 e 7 Plus)**

    ||3x|
    |---|---|
    |Icona dell'applicazione|180x180|
    |In primo piano|120x120|
    |Impostazioni|87 x 87|

- **iPhone: iOS 7 e 8**

    ||1x|2x|
    |---|---|---|
    |Icona dell'applicazione|60x60<sup>1</sup>|120x120|
    |In primo piano|40x40<sup>2</sup>|80x80|
    |Impostazioni|-|-|

- **iPhone: iOS 5 e 6**

    ||1x|2x|
    |---|---|---|
    |Icona dell'applicazione|57x57|114x114|
    |In primo piano|29x29|58x58|
    |Impostazioni|29x29<sup>3, 4</sup>|58x58<sup>3, 4</sup>|

### <a name="ipad-icon-sizes"></a>Dimensioni delle icone iPad

- **iPad: iOS 9 & 10**

    ||2 x (iPad Pro)|
    |---|---|
    |Icona dell'applicazione|167x167<sup>6</sup>|
    |In primo piano|120x120<sup>6</sup>|
    |Impostazioni|58x58<sup>5</sup>|

- **iPad: iOS 7 e 8**

    ||1x|2x|
    |---|---|---|
    |Icona dell'applicazione|76x76|152x152|
    |In primo piano|40x40|80x80|
    |Impostazioni|-|-|

- **iPad: iOS 5 & 6**

    ||1x|2x|
    |---|---|---|
    |Icona dell'applicazione|72x72|144x144|
    |In primo piano|50x50|100x100|
    |Impostazioni|29x29<sup>3, 5</sup>|58x58<sup>3, 5</sup>|

 1. Sia Visual Studio per Mac e Xcode non supporterà più l'impostazione di immagine x 1 per iOS 7.
 2. Impostazione di un'immagine 1x per iOS 7 non è supportato quando si usa cataloghi Asset.
 3. iOS 7 e 8 di usare le stesse dimensioni dell'immagine come iOS 5 e 6.
 4. Usa le stesse immagini e le dimensioni dell'icona in primo piano.
 5. Usa le stesse icone di dimensioni come iPhone.
 6. Supportato solo con set di immagini di catalogo di Asset.
 
 Per altre informazioni sulle icone, vedere di Apple [icona e dimensioni delle immagini](https://developer.apple.com/library/ios/documentation/UserExperience/Conceptual/MobileHIG/IconMatrix.html#//apple_ref/doc/uid/TP40006556-CH27-SW1) documentazione.

<a name="managing" />

## <a name="managing-icons-with-asset-catalogs"></a>La gestione delle icone con cataloghi Asset

Per le icone, una speciale `AppIcon` può essere aggiunto al set di immagini di `Assets.xcassets` file nel progetto dell'app. Tutte le versioni dell'immagine necessaria per supportare tutte le risoluzioni sono inclusi nel _xcasset_ e raggruppati insieme. Un editor di speciale in Visual Studio per Mac consente allo sviluppatore di includere e graficamente queste immagini di installazione.

Per usare un catalogo Asset, eseguire le operazioni seguenti:

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

1. Fare doppio clic il `Info.plist` del file nei **Esplora soluzioni** per aprirlo e modificarlo.
2. Scorrere verso il basso il **icone dell'App** sezione.
3. Dal **origine** elenco a discesa elenco, assicurarsi che **AppIcon** è selezionato: 

    ![](app-icons-images/migrate01.png "Verificare che sia selezionata AppIcon")
4. Dal **Esplora soluzioni**, fare doppio clic il `Assets.xcassets` file per aprirlo e modificarlo: 

    ![](app-icons-images/asset01.png "Il file assets. xcassets in Esplora soluzioni")
5. Selezionare `AppIcon` dall'elenco degli asset per visualizzare il `Icon Editor`:

    ![](app-icons-images/asset02.png "L'editor AppIcon")
6. Fare clic sul tipo di icona specificato e selezionare un file di immagine per la dimensione o il tipo richiesto o trascinare in un'immagine da una cartella e ma rilasciarlo sulla dimensione desiderata.
7. Scegliere il **Open** per includere l'immagine nel progetto e impostarla di xcasset.
8. Ripetere per tutte le immagini necessarie.

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. Fare doppio clic il **Info. plist** del file nei **Esplora soluzioni**:

    ![](app-icons-images/icon01w.png "Selezionare file Info. plist")
2. Fare clic sui **asset visivi** scheda e fare clic sui **Usa catalogo Asset** pulsante sotto **icone dell'App**: 

    ![](app-icons-images/icon02w.png "Selezionare la scheda asset visivi")
4. Dal **Esplora soluzioni**, espandere il **catalogo Asset** cartella: 

    ![](app-icons-images/image009.png "Espandere la cartella catalogo Asset")
5. Fare doppio clic il **supporti** file per aprirlo nell'editor: 

    ![](app-icons-images/image010.png "Aprire il file multimediale nell'editor")
6. Sotto il **riquadro delle proprietà** lo sviluppatore può selezionare i diversi tipi e dimensioni delle icone necessarie.
7. Fare clic sul tipo di icona specificato e selezionare un file di immagine per la dimensione del testo obbligatorio.
8. Scegliere il **Open** per includere l'immagine nel progetto e impostarla di xcasset.
9. Ripetere per tutte le immagini necessarie.

-----

Questo è il metodo preferito di inclusi e gestione degli asset di immagine che verranno usati per specificare le icone dell'applicazione, Spotlight e impostazioni per un'app.

### <a name="migrating-from-infoplist-to-asset-catalogs"></a>Migrazione da Info. plist a cataloghi Asset

Per un'app xamarin. IOS esistente usando il `Info.plist` file per gestire le relative icone, è altamente consigliabile che lo sviluppatore di passare a usare il `AppIcons` Asset immagine all'interno di `Assets.xcassets`.

Seguire questa procedura:

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

1. Fare doppio clic il `Info.plist` del file nei **Esplora soluzioni** per aprirlo e modificarlo.
2. Scorrere verso il basso il **icone dell'App** sezione.
3. Dal **origine** elenco a discesa, seleziona **eseguire la migrazione a cataloghi Asset**: 

    ![](app-icons-images/migrate02.png "Selezionare la migrazione a cataloghi Asset")
4. Tutte le icone esistenti definite nel `Info.plist` file verrà migrati a una `AppIcons` immagine Set aggiunto a `Assets.xcassets`: 

     ![](app-icons-images/migrate03.png "Il Set di immagini AppIcons nell'assets. xcassets")

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. Fare doppio clic il `Info.plist` del file nei **Esplora soluzioni** per aprirlo e modificarlo.
2. Fare clic su iPhone sezione icone: 

    ![](app-icons-images/image007.png "Editor di icone iPhone Rhe")
3. Scorrere verso il basso il **icone** sezione.
4. Dal **catalogo di Asset** elenco a discesa, seleziona **cataloghi Asset usare**.
5. Tutte le icone esistente definito nel `Info.plist` file verrà migrati a una `Images` aggiunto al set `Assets.xcassets`.
6. Salvare le modifiche apportate al file `Info.plist`.

-----

<a name="itunes" />

## <a name="itunes-artwork"></a>Illustrazione di iTunes

Se si usa il metodo Ad-Hoc della distribuzione dell'app (sia per gli utenti aziendali o per la fase di beta testing su dispositivi reali), lo sviluppatore deve inoltre includere un 512x512 e un'immagine di 1024 x 1024 che verrà usata per rappresentare l'app in iTunes.

Per specificare l'illustrazione di iTunes, seguire questa procedura:

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

1. Fare doppio clic il `Info.plist` del file nei **Esplora soluzioni** per aprirlo e modificarlo.
2. Scorrere fino al **illustrazione di iTunes** sezione dell'editor: 

    ![](app-icons-images/itunes01.png "Scorrere fino alla sezione di disegno dell'editor di iTunes")
3. Per un'immagine mancante, fare clic sull'anteprima nell'editor, selezionare il file di immagine per l'illustrazione di iTunes desiderata dalla finestra di dialogo Apri File e fare clic sui **OK** pulsante.
4. Ripetere questo passaggio finché non restituiscono tutti necessari immagini sono state specificate per l'app.

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. Fare doppio clic il `Info.plist` del file nei **Esplora soluzioni** per aprirlo e modificarlo.

2. Fare clic sui **asset visivi** scheda ed espandere le **illustrazione di iTunes**: 

    ![](app-icons-images/itunes01w.png "Modifica in Visual Studio illustrazione di iTunes")
4. Per un'immagine mancante, fare clic sull'anteprima nell'editor, selezionare il file di immagine per l'illustrazione di iTunes desiderata dalla finestra di dialogo Apri File e fare clic sui **aperto** pulsante.
5. Ripetere questo passaggio finché non restituiscono tutti necessari immagini sono state specificate per l'app.

-----

## <a name="related-links"></a>Collegamenti correlati

- [Utilizzo di immagini (esempio)](https://developer.xamarin.com/samples/WorkingWithImages/)
- [Hello, iPhone](~/ios/get-started/hello-ios/index.md)
- [Icona personalizzata e linee guida per la creazione di immagini](http://developer.apple.com/library/ios/#documentation/UserExperience/Conceptual/MobileHIG/IconsImages/IconsImages.html))
