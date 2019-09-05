---
title: Icone dell'applicazione in Novell. iOS
description: "Questo documento descrive come usare varie icone dell'applicazione in Novell. iOS: icona dell'applicazione stessa, icone di Spotlight, icone delle impostazioni e immagini di iTunes."
ms.prod: xamarin
ms.assetid: B7791574-4A0F-4CB6-8C18-36D40B5C91EB
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 05/22/2017
ms.openlocfilehash: a44f0921b959c6b8fd6f076fd119ea52345f821f
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/04/2019
ms.locfileid: "70282648"
---
# <a name="application-icons-in-xamarinios"></a>Icone dell'applicazione in Novell. iOS

Gli argomenti seguenti vengono descritti nel dettaglio:

- [Icone delle applicazioni, dei riflettori e delle impostazioni](#icon-types) : i diversi tipi di icone necessari per un'app iOS.
- [Gestione delle icone con cataloghi di asset](#managing) -gestione delle icone delle applicazioni con i cataloghi di asset.
- [artwork di iTunes](#itunes) : fornire la grafica di iTunes necessaria per il metodo ad hoc per la distribuzione dell'applicazione.

<a name="icon-types" />

## <a name="application-spotlight-and-settings-icons"></a>Icone di applicazione, Spotlight e impostazioni

Nello stesso modo in cui un'app Novell. iOS può usare asset di immagine per i controlli dell'interfaccia utente e come icone di documento, gli asset di immagine possono essere usati per fornire le icone dell'applicazione. Gli screenshot seguenti di un iPad illustrano i tre usi delle icone in iOS:

- **Icona dell'applicazione** : ogni app iOS deve definire un'icona dell'applicazione. Questa è l'icona che l'utente toccherà dalla schermata iniziale di iOS per avviare l'app. Questa icona viene inoltre utilizzata da Game Center, se applicabile. Esempio: 

    [![](app-icons-images/000.png "Icona dell'applicazione")](app-icons-images/000-full.png#lightbox)
- **Icona in evidenza** : ogni volta che l'utente immette il nome di un'app in una ricerca Spotlight, viene visualizzata questa icona. Esempio: 

    [![](app-icons-images/000a.png "Icona in evidenza")](app-icons-images/000a-full.png#lightbox)
- **Icona delle impostazioni** : se l'utente immette l'app **Impostazioni** nel dispositivo iOS, questa icona verrà visualizzata alla fine dell'elenco **delle impostazioni** per l'app. Esempio: 

    [![](app-icons-images/000b.png "Icona impostazioni")](app-icons-images/000b-full.png#lightbox)

Per supportare tutti i tipi di icona richiesti da un'app Novell. iOS destinata a iOS 5 tramite iOS 9 (o versione successiva), saranno necessarie le risoluzioni e le dimensioni degli asset immagine seguenti:

### <a name="iphone-icon-sizes"></a>Dimensioni delle icone iPhone

- **iPhone: iOS 9 & 10 (iPhone 6 & 7 Plus)**

    ||3x|
    |---|---|
    |Icona dell'applicazione|180x180|
    |Riflettori|120x120|
    |Impostazioni|87x87|

- **iPhone: iOS 7 & 8**

    ||1x|2x|
    |---|---|---|
    |Icona dell'applicazione|60x60<sup>1</sup>|120x120|
    |Riflettori|40x40<sup>2</sup>|80x80|
    |Impostazioni|-|-|

- **iPhone: iOS 5 & 6**

    ||1x|2x|
    |---|---|---|
    |Icona dell'applicazione|57x57|114x114|
    |Riflettori|29x29|58x58|
    |Impostazioni|29x29<sup>3, 4</sup>|58x58<sup>3, 4</sup>|

### <a name="ipad-icon-sizes"></a>Dimensioni delle icone iPad

- **iPad: iOS 9 & 10**

    ||2x (iPad Pro)|
    |---|---|
    |Icona dell'applicazione|167x167<sup>6</sup>|
    |Riflettori|120x120<sup>6</sup>|
    |Impostazioni|58x58<sup>5</sup>|

- **iPad: iOS 7 & 8**

    ||1x|2x|
    |---|---|---|
    |Icona dell'applicazione|76x76|152x152|
    |Riflettori|40x40|80x80|
    |Impostazioni|-|-|

- **iPad: iOS 5 & 6**

    ||1x|2x|
    |---|---|---|
    |Icona dell'applicazione|72x72|144x144|
    |Riflettori|50x50|100x100|
    |Impostazioni|29x29<sup>3, 5</sup>|58x58<sup>3, 5</sup>|

 1. Sia Visual Studio per Mac che Xcode non supportano più l'impostazione dell'immagine 1x per iOS 7.
 2. L'impostazione di un'immagine 1x per iOS 7 non è supportata quando si usano cataloghi asset.
 3. iOS 7 & 8 usano le stesse dimensioni di immagine di iOS 5 & 6.
 4. Usa le stesse immagini e dimensioni dell'icona in primo piano.
 5. Usa le stesse icone di dimensioni dell'iPhone.
 6. Supportato solo con set di immagini del catalogo asset.
 
 Per altre informazioni sulle icone, vedere la documentazione relativa alle [dimensioni delle immagini e](https://developer.apple.com/library/ios/documentation/UserExperience/Conceptual/MobileHIG/IconMatrix.html#//apple_ref/doc/uid/TP40006556-CH27-SW1) delle icone di Apple.

<a name="managing" />

## <a name="managing-icons-with-asset-catalogs"></a>Gestione delle icone con i cataloghi asset

Per le `AppIcon` `Assets.xcassets` icone, è possibile aggiungere un set di immagini speciale al file nel progetto dell'app. Tutte le versioni dell'immagine necessarie per supportare tutte le risoluzioni sono incluse in _xcasset_ e raggruppate insieme. Un editor speciale in Visual Studio per Mac consente allo sviluppatore di includere e configurare graficamente tali immagini.

Per usare un catalogo asset, eseguire le operazioni seguenti:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

1. Fare doppio clic sul `Info.plist` file nella **Esplora soluzioni** per aprirlo per la modifica.
2. Scorrere verso il basso fino alla sezione **icone app** .
3. Dall'elenco a discesa **origine** verificare che sia selezionata l'opzione **AppIcon** : 

    ![](app-icons-images/migrate01.png "Verificare che AppIcon sia selezionato")
4. Dal **Esplora soluzioni**fare doppio clic sul `Assets.xcassets` file per aprirlo per la modifica: 

    ![](app-icons-images/asset01.png "Il file assets. xcassets nel Esplora soluzioni")
5. Selezionare `AppIcon` dall'elenco degli asset per `Icon Editor`visualizzare:

    ![](app-icons-images/asset02.png "Editor AppIcon")
6. Fare clic su tipo di icona specificato e selezionare un file di immagine per il tipo o le dimensioni richieste o trascinare un'immagine da una cartella e rilasciarla sulla dimensione desiderata.
7. Fare clic sul pulsante **Apri** per includere l'immagine nel progetto e impostarla in xcasset.
8. Ripetere per tutte le immagini necessarie.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. Fare doppio clic sul file **info. plist** nel **Esplora soluzioni**:

    ![](app-icons-images/icon01w.png "Selezionare Info. plist")
2. Fare clic sulla scheda **Asset visivi** e fare clic sul pulsante **USA Catalogo asset** in **icone app**: 

    ![](app-icons-images/icon02w.png "Selezionare la scheda Asset visivi")
3. Dalla **Esplora soluzioni**espandere la cartella del **Catalogo asset** : 

    ![](app-icons-images/image009.png "Espandere la cartella del catalogo asset")
4. Fare doppio clic sul file **multimediale** per aprirlo nell'Editor: 

    ![](app-icons-images/image010.png "Aprire il file multimediale nell'editor")
5. In **Esplora proprietà** lo sviluppatore può selezionare i diversi tipi e dimensioni delle icone necessarie.
6. Fare clic su tipo di icona specificato e selezionare un file di immagine per il tipo o le dimensioni richieste.
7. Fare clic sul pulsante **Apri** per includere l'immagine nel progetto e impostarla in xcasset.
8. Ripetere per tutte le immagini necessarie.

-----

Questo è il metodo preferito per l'inclusione e la gestione di asset di immagini che verranno usati per fornire icone di applicazione, Spotlight e impostazioni per un'app.

### <a name="migrating-from-infoplist-to-asset-catalogs"></a>Migrazione dal file INFO. plist ai cataloghi asset

Per un'app Novell. iOS esistente che usa `Info.plist` il file per gestire le icone, è consigliabile che lo sviluppatore lo commuti per usare l' `AppIcons` asset di immagine all'interno `Assets.xcassets`di.

Seguire questa procedura:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

1. Fare doppio clic sul `Info.plist` file nella **Esplora soluzioni** per aprirlo per la modifica.
2. Scorrere verso il basso fino alla sezione **icone app** .
3. Dall'elenco a discesa **origine** selezionare **Esegui migrazione a cataloghi asset**: 

    ![](app-icons-images/migrate02.png "Selezionare Esegui migrazione a cataloghi asset")
4. Viene eseguita la migrazione di tutte `Info.plist` le icone esistenti definite nel file a `AppIcons` un set di immagini `Assets.xcassets`aggiunto a: 

     ![](app-icons-images/migrate03.png "Immagine di AppIcons impostata in assets. xcassets")

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. Fare doppio clic sul `Info.plist` file nella **Esplora soluzioni** per aprirlo per la modifica.
2. Fare clic sulla sezione icone iPhone: 

    ![](app-icons-images/image007.png "Editor icone iPhone")
3. Scorrere verso il basso fino alla sezione **Icone** .
4. Dall'elenco a discesa **Catalogo asset** selezionare **USA cataloghi asset**.
5. Viene eseguita la migrazione di tutte `Info.plist` le icone esistenti definite nel file a `Images` un set aggiunto `Assets.xcassets`a.
6. Salvare le modifiche apportate al file `Info.plist`.

-----

<a name="itunes" />

## <a name="itunes-artwork"></a>Illustrazione di iTunes

Se si usa il metodo ad hoc per la distribuzione dell'app (per gli utenti aziendali o per il test beta nei dispositivi reali), lo sviluppatore deve anche includere un 512x512 e un'immagine 1024x1024 che verranno usati per rappresentare l'app in iTunes.

Per specificare l'illustrazione di iTunes, seguire questa procedura:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

1. Fare doppio clic sul `Info.plist` file nella **Esplora soluzioni** per aprirlo per la modifica.
2. Scorrere fino alla sezione **iTunes artwork** dell'Editor: 

    ![](app-icons-images/itunes01.png "Scorrere fino alla sezione iTunes artwork dell'editor")
3. Per le immagini mancanti, fare clic sull'anteprima nell'editor, selezionare il file di immagine per la grafica iTunes desiderata dalla finestra di dialogo Apri file e fare clic su **OK** .
4. Ripetere questo passaggio fino a quando non sono state specificate tutte le immagini necessarie per l'app.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. Fare doppio clic sul `Info.plist` file nella **Esplora soluzioni** per aprirlo per la modifica.

2. Fare clic sulla scheda **Asset visivi** ed espandere la **grafica iTunes**: 

    ![](app-icons-images/itunes01w.png "Modifica di iTunes artwork in Visual Studio")
3. Per le immagini mancanti, fare clic sull'anteprima nell'editor, selezionare il file di immagine per la grafica iTunes desiderata dalla finestra di dialogo Apri file e fare clic sul pulsante **Apri** .
4. Ripetere questo passaggio fino a quando non sono state specificate tutte le immagini necessarie per l'app.

-----

## <a name="related-links"></a>Collegamenti correlati

- [Uso delle immagini (esempio)](https://docs.microsoft.com/samples/xamarin/ios-samples/workingwithimages)
- [Hello, iPhone](~/ios/get-started/hello-ios/index.md)
- [Linee guida per la creazione di icone e immagini personalizzate](https://developer.apple.com/library/ios/#documentation/UserExperience/Conceptual/MobileHIG/IconsImages/IconsImages.html))
