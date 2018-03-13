---
title: Icone applicazioni
description: Questo articolo viene illustrato come inclusi e la gestione di un asset di immagine in un'app xamarin da utilizzare come icona dell'App.
ms.topic: article
ms.prod: xamarin
ms.assetid: B7791574-4A0F-4CB6-8C18-36D40B5C91EB
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 05/22/2017
ms.openlocfilehash: df9059b0e64b4a05b554f25b5f9d7f6031406633
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/09/2018
---
# <a name="application-icons"></a>Icone applicazioni

_Questo articolo viene illustrato come inclusi e la gestione di un asset di immagine in un'app xamarin da utilizzare come icona dell'App._

Gli argomenti seguenti vengono descritti nel dettaglio:

* [Applicazione, in primo piano e le icone di impostazioni](#icon-types) -i diversi tipi di icone, necessarie per un'app iOS.
* [La gestione di icone con i cataloghi Asset](#managing) : la gestione delle icone di applicazioni utilizzando Asset cataloghi.
* [iTunes grafica](#itunes) -fornendo iTunes obbligatorio grafica per il metodo Ad Hoc di recapito dell'applicazione.

<a name="icon-types" />

## <a name="application-spotlight-and-settings-icons"></a>Applicazione, in primo piano e le icone di impostazioni

Nello stesso modo che un'app xamarin. IOS è possibile utilizzare gli asset delle immagini per i controlli dell'interfaccia utente e come icone di documento, è possono utilizzare gli asset delle immagini per fornire le icone di applicazione. Le schermate seguenti da un iPad illustrati gli utilizzi di tre delle icone in iOS:

- **Icona dell'applicazione** -tutte le applicazioni iOS è necessario definire un'icona dell'applicazione. Questa è l'icona che l'utente verrà toccare dalla schermata iniziale di iOS per avviare l'app. Inoltre, questa icona viene utilizzata da Game Center, se applicabile. Esempio: 

    [![](app-icons-images/000.png "Icona dell'applicazione")](app-icons-images/000-full.png#lightbox)
- **Icona riflettore** : ogni volta che l'utente immette il nome di un'app in una ricerca Spotlight, questa icona viene visualizzata. Esempio: 

    [![](app-icons-images/000a.png "Icona Spotlight")](app-icons-images/000a-full.png#lightbox)
- **Icona impostazioni** : se l'utente immette il **impostazioni** app sul proprio dispositivo iOS, questa icona verrà visualizzata alla fine del **impostazioni** elenco per l'app. Esempio: 

    [![](app-icons-images/000b.png "Icona impostazioni")](app-icons-images/000b-full.png#lightbox)

La seguente immagine asset risoluzioni e dimensioni saranno necessarie per supportare tutti i tipi di icona richiesto da un'app xamarin destinazione iOS 5 tramite iOS 9 (o versione successiva):

<table cellpadding="7" cellspacing="0" width="100%">
    <tr valign="top">
        <td width="200" style="border-width: 0px;"></td>
        <td colspan="5" align="center" bgcolor="#F0F0F0"><b>iPhone</b></td>
    </tr>
    <tr valign="center">
        <td width="200" style="border-width: 0px;"></td>
        <td colspan="2" align="center" bgcolor="#F9F9F9"><b>iOS 5 & 6</b></td>
        <td colspan="2" align="center" bgcolor="#F9F9F9"><b>iOS 7 & 8</b></td>
        <td align="center" bgcolor="#F9F9F9"><b>iOS 9 & 10<b><br/><i>(iPhone 6 e 7 più)</i></td>
    </tr>
    <tr valign="top" bgcolor="#F0F0F0">
        <td width="200" align="center"><b>Tipo di icona</b></td>
        <td align="center"><b>1x</b></td>
        <td align="center"><b>2x</b></td>
        <td align="center"><b>1x</b></td>
        <td align="center"><b>2x</b></td>
        <td align="center"><b>3x</b></td>
    </tr>
    <tr valign="top">
        <td width="200" bgcolor="#F9F9F9" align="right">Icona dell'applicazione</td>
        <td align="center">57x57</td>
        <td align="center">114x114</td>
        <td align="center" style="color:#BBBBBB;">60x60<sup>(1)</sup></td>
        <td align="center">120x120</td>
        <td align="center">180x180</td>
    </tr>
    <tr valign="top">
        <td width="200" bgcolor="#F9F9F9" align="right">In primo piano</td>
        <td align="center">29x29</td>
        <td align="center">58x58</td>
        <td align="center" style="color:#BBBBBB;">40x40<sup>(2)</sup></td>
        <td align="center">80x80</td>
        <td align="center">120x120</td>
    </tr>
    <tr valign="top">
        <td width="200" bgcolor="#F9F9F9" align="right">Impostazioni</td>
        <td align="center" style="color:#BBBBBB;">29x29<sup>(3)(4)</sup></td>
        <td align="center" style="color:#BBBBBB;">58x58<sup>(3)(4)</sup></td>
        <td align="center">-</td>
        <td align="center">-</td>
        <td align="center">87x87</td>
    </tr>
</table>

<table cellpadding="7" cellspacing="0" width="100%">
    <tr valign="top">
        <td width="200" style="border-width: 0px;"></td>
        <td colspan="5" align="center" bgcolor="#F0F0F0"><b>iPad</b></td>
    </tr>
    <tr valign="center">
        <td width="200" style="border-width: 0px;"></td>
        <td colspan="2" align="center" bgcolor="#F9F9F9"><b>iOS 5 & 6</b></td>
        <td colspan="2" align="center" bgcolor="#F9F9F9"><b>iOS 7 & 8</b></td>
        <td colspan="1" align="center" bgcolor="#F9F9F9"><b>iOS&nbsp;9 & 10</b></td>
    </tr>
    <tr valign="top" bgcolor="#F0F0F0">
        <td width="200" align="center"><b>Tipo di icona</b></td>
        <td align="center"><b>1x</b></td>
        <td align="center"><b>2x</b></td>
        <td align="center"><b>1x</b></td>
        <td align="center"><b>2x</b></td>
        <td align="center"><b>2x<br/>iPad&nbsp;Pro</b></td>
    </tr>
    <tr valign="top">
        <td width="200" bgcolor="#F9F9F9" align="right">Icona dell'applicazione</td>
        <td align="center">72x72</td>
        <td align="center">144x144</td>
        <td align="center">76x76</td>
        <td align="center">152x152</td>
        <td align="center">167x167<sup>(6)</sup></td>
    </tr>
    <tr valign="top">
        <td width="200" bgcolor="#F9F9F9" align="right">In primo piano</td>
        <td align="center">50x50</td>
        <td align="center">100x100</td>
        <td align="center">40x40</td>
        <td align="center">80x80</td>
        <td align="center" style="color:#BBBBBB;">120x120<sup>(5)</sup></td>
    </tr>
    <tr valign="top">
        <td width="200" bgcolor="#F9F9F9" align="right">Impostazioni</td>
        <td align="center" style="color:#BBBBBB;">29x29<sup>(3)(5)</sup></td>
        <td align="center" style="color:#BBBBBB;">58x58<sup>(3)(5)</sup></td>
        <td align="center">-</td>
        <td align="center">-</td>
        <td align="center" style="color:#BBBBBB;">58x58<sup>(5)</sup></td>
    </tr>
</table>

1. _Sia Visual Studio per Mac e Xcode non supporta più l'impostazione 1 immagine x per iOS 7._
2. _Impostazione di un'immagine di 1 x per iOS 7 non è supportato quando si utilizza Asset cataloghi._
3. _iOS 7 e 8 di usare le stesse dimensioni dell'immagine come iOS 5 e 6._
4. _Utilizza lo stesso immagini e le dimensioni dell'icona in primo piano._
5. _Usa le stesse icone di dimensioni come iPhone._
6. _Supportato solo con set di immagini di catalogo di Asset._

Per ulteriori informazioni sulle icone, vedere Apple [icona e dimensioni delle immagini](https://developer.apple.com/library/ios/documentation/UserExperience/Conceptual/MobileHIG/IconMatrix.html#//apple_ref/doc/uid/TP40006556-CH27-SW1) documentazione.

<a name="managing" />

## <a name="managing-icons-with-asset-catalogs"></a>La gestione delle icone con i cataloghi Asset

Per le icone, una speciale `AppIcons` set immagine può essere aggiunto al `Assets.xcassets` file nel progetto dell'app. Tutte le versioni dell'immagine devono supportare tutte le soluzioni sono inclusi nel _xcasset_ e raggruppati insieme. Un editor di Visual Studio per Mac speciale consente allo sviluppatore consente di includere queste immagini di installazione graficamente.

Per utilizzare un catalogo di Asset, eseguire le operazioni seguenti:

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

1. Fare doppio clic su di `Info.plist` file nel **Esplora** per aprirlo e modificarlo.
2. Scorrere verso il basso il **icone App** sezione.
3. Dal **origine** elenco a discesa elenco, verificare **AppIcons** è selezionata: 

    ![](app-icons-images/migrate01.png "Assicurarsi che sia selezionato AppIcons")
4. Dal **Esplora**, fare doppio clic su di `Assets.xcassets` file per aprirlo e modificarlo: 

    ![](app-icons-images/asset01.png "Il file Assets.xcassets in Esplora soluzioni")
5. Selezionare `AppIcons` dall'elenco degli asset per visualizzare il `Icon Editor`: 

    ![](app-icons-images/asset02.png "L'editor AppIcons")
6. Scegliere il tipo di icona specificato e selezionare un file di immagine per la dimensione del testo obbligatorio o trascinare in un'immagine da una cartella e rilasciarla sulla dimensione desiderata.
7. Fare clic su di **aprire** per includere l'immagine nel progetto e impostarlo nel xcasset.
8. Ripetere l'operazione per tutte le immagini necessarie.

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. Fare doppio clic su di **Info. plist** file nel **Esplora**:

    ![](app-icons-images/icon01w.png "Selezionare Info. plist")
2. Fare clic sul **asset visivi** scheda, fare clic su di **utilizzare Asset Catalog** pulsante sotto **icone App**: 

    ![](app-icons-images/icon02w.png "Selezionare la scheda di asset visivi")
4. Dal **Esplora**, espandere il **catalogo di Asset** cartella: 

    ![](app-icons-images/image009.png "Espandere la cartella del catalogo di Asset")
5. Fare doppio clic su di **Media** file per aprirlo nell'editor: 

    ![](app-icons-images/image010.png "Aprire il file di supporto nell'editor")
6. Sotto il **Esplora proprietà** lo sviluppatore può selezionare i diversi tipi e le dimensioni delle icone necessarie.
7. Scegliere il tipo di icona specificato e selezionare un file di immagine per la dimensione del testo obbligatorio.
8. Fare clic su di **aprire** per includere l'immagine nel progetto e impostarlo nel xcasset.
9. Ripetere l'operazione per tutte le immagini necessarie.

-----

Questo è il metodo preferito di inclusi e gestione delle risorse di immagine che verranno utilizzati per fornire le icone di applicazione, in primo piano e le impostazioni per un'app.

### <a name="migrating-from-infoplist-to-asset-catalogs"></a>Migrazione da Info. plist a cataloghi Asset

Per un'app xamarin esistente mediante il `Info.plist` file per la gestione di icone, è altamente consigliabile che lo sviluppatore di passare da utilizzare il `AppIcons` Asset immagine all'interno di `Assets.xcassets`.

Seguire questa procedura:

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

1. Fare doppio clic su di `Info.plist` file nel **Esplora** per aprirlo e modificarlo.
2. Scorrere verso il basso il **icone App** sezione.
3. Dal **origine** elenco a discesa, seleziona **eseguire la migrazione a cataloghi Asset**: 

    ![](app-icons-images/migrate02.png "Selezionare la migrazione a cataloghi Asset")
4. Le icone esistente definita nel `Info.plist` verrà migrati al file un `AppIcons` immagine impostata aggiunti `Assets.xcassets`: 

     ![](app-icons-images/migrate03.png "Impostare l'immagine AppIcons nel Assets.xcassets")

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. Fare doppio clic su di `Info.plist` file nel **Esplora** per aprirlo e modificarlo.
2. Fare clic su iPhone sezione icone: 

    ![](app-icons-images/image007.png "Editor di icone Rhe iPhone")
3. Scorrere verso il basso il **icone** sezione.
4. Dal **catalogo di Asset** elenco a discesa, seleziona **utilizzare Asset cataloghi**.
5. Le icone esistente definita nel `Info.plist` verrà migrati al file un `Images` aggiunto al set `Assets.xcassets`.
6. Salvare le modifiche apportate al file `Info.plist`.

-----

<a name="itunes" />

## <a name="itunes-artwork"></a>Illustrazione di iTunes

Se tramite il metodo Ad Hoc di fornire l'app (per gli utenti aziendali o di test della versione beta su dispositivi), lo sviluppatore deve inoltre includere un 512 x 512 e un'immagine di 1024 x 1024 che verrà utilizzata per rappresentare l'app in iTunes.

Per specificare l'illustrazione di iTunes, seguire questa procedura:

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

1. Fare doppio clic su di `Info.plist` file nel **Esplora** per aprirlo e modificarlo.
2. Scorrere verso il **iTunes grafica** sezione dell'editor: 

    ![](app-icons-images/itunes01.png "Scorrere fino alla sezione grafica dell'editor iTunes")
3. Per le immagini mancante, fare clic su Anteprima nell'editor, selezionare il file di immagine per il disegno iTunes desiderato nella finestra di dialogo Apri File e fare clic su di **OK** pulsante.
4. Ripetere questo passaggio finché tutti necessari immagini sono state specificate per l'app.

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. Fare doppio clic su di `Info.plist` file nel **Esplora** per aprirlo e modificarlo.

2. Fare clic su di **asset visivi** scheda ed espandere il **iTunes grafica**: 

    ![](app-icons-images/itunes01w.png "Modifica iTunes grafica in Visual Studio")
4. Per le immagini mancante, fare clic su Anteprima nell'editor, selezionare il file di immagine per il disegno iTunes desiderato nella finestra di dialogo Apri File e fare clic su di **aprire** pulsante.
5. Ripetere questo passaggio finché tutti necessari immagini sono state specificate per l'app.

-----

## <a name="related-links"></a>Collegamenti correlati

- [Utilizzo di immagini (esempio)](https://developer.xamarin.com/samples/WorkingWithImages/)
- [Hello, iPhone](~/ios/get-started/hello-ios/index.md)
- [Icona personalizzata e linee guida per la creazione di immagini](http://developer.apple.com/library/ios/#documentation/UserExperience/Conceptual/MobileHIG/IconsImages/IconsImages.html))
