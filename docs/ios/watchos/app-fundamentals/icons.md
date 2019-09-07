---
title: Uso delle icone watchos in Novell
description: Questo documento descrive le varie icone necessarie per un'applicazione watchos e come configurare una soluzione per includere queste icone.
ms.prod: xamarin
ms.assetid: EE3D45BD-8091-4C04-BA83-371371D8BEB9
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 07/26/2018
ms.openlocfilehash: e0bf9ec1553e6638398695157a11242b9885b168
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/06/2019
ms.locfileid: "70768104"
---
# <a name="working-with-watchos-icons-in-xamarin"></a>Uso delle icone watchos in Novell

Per le soluzioni Apple Watch sono necessari due set di icone:

- Icone dell'app iOS che verranno visualizzate nell'iPhone.
- Apple Watch icone di cui verrà eseguito il rendering in un cerchio nel menu espressioni di controllo e nelle schermate di notifica. L'icona dell'app Watch viene visualizzata anche nell'app [Apple Watch](~/ios/watchos/app-fundamentals/settings.md) iOS.

## <a name="apple-watch-icons"></a>Icone Apple Watch

| | | |
|-|-|-|
|Icona dell'app iOS|Viene visualizzato nell'iPhone e avvia l'app padre|![icona dell'app iOS](icons-images/icon-ios.png)|
|Icona dell'app Watch|Viene visualizzato nella schermata iniziale Apple Watch|![icona dell'app watchos](icons-images/icon-home.png)|
||Visualizzato nelle notifiche di controllo|![icona di notifica watchos](icons-images/notification-icon.png)|
||Viene visualizzato nell' [App Apple Watch iOS](~/ios/watchos/app-fundamentals/settings.md)|![icona dell'app Watch iOS](icons-images/watch-app-sml.png)|

## <a name="configuring-your-solution"></a>Configurazione della soluzione

Per assicurarsi che l'app iOS e l'app Watch mostrino entrambi il nome e l'icona corretti, seguire queste istruzioni per ogni progetto:

### <a name="ios-app"></a>App iOS

Vedere la [Guida alle icone delle applicazioni iOS](~/ios/app-fundamentals/images-icons/app-icons.md) per assicurarsi che le icone dell'app iOS siano configurate correttamente.

#### <a name="infoplist"></a>Info. plist

La stringa visualizzata accanto all'app Watch nell' [app impostazioni Apple Watch](~/ios/watchos/app-fundamentals/settings.md) è configurata nel file **info. plist dell'app iOS**.

Verificare che il file **info. plist** disponga `CFBundleName` di una chiave e di un valore (Nota: questa `CFBundleDisplayName`operazione è diversa da, è possibile avere entrambe):

```xml
<key>CFBundleName</key>
<string>Your App Name</string>
```

### <a name="apple-watch-app"></a>App Apple Watch

Una volta configurate le icone per l' [app padre](~/ios/watchos/app-fundamentals/parent-app.md) , è necessario aggiungere un'icona dell'applicazione Catalogo asset all'app Watch.

1. Fare clic con il pulsante destro del mouse sul progetto Watch app e selezionare **file > aggiungi > nuovo file... > il catalogo asset > iOS** per aggiungere un catalogo asset al progetto.

    ![](icons-images/newasset.png "Aggiungere un catalogo asset al progetto")

2. Fare doppio clic sul file **AppIcon. appiconset/Contents. JSON**

    ![](icons-images/xcassets-iconset-sml.png "Contenuto di AppIcon")

3. Aggiungere tutte le immagini watchos, come illustrato in questo screenshot:

    [![](icons-images/appicons-sml.png "Aggiungi tutte le immagini watchos, come illustrato in questa schermata")](icons-images/appicons.png#lightbox)

    Vedere le [linee guida sulle icone di Apple](https://developer.apple.com/design/human-interface-guidelines/watchos/icons-and-images/menu-icons/) per le dimensioni richieste (le dimensioni sono visualizzate anche sullo schermo). Tenere presente che queste icone verranno ritagliate automaticamente per il rendering in un cerchio.

    L'elenco di icone dovrebbe avere un aspetto simile al seguente:

    ![](icons-images/xcassets-complete-sml.png "Elenco di icone nell'Esplora soluzioni")

4. Per assicurarsi che il catalogo asset sia incluso nell'app, aggiungere la chiave e il valore seguenti al file **info. plist dell'app Watch**:

    ```xml
    <key>XSAppIconAssets</key>
    <string>Images.xcassets/AppIcon.appiconset</string>
    ```

È possibile verificare che le icone siano configurate correttamente controllando l' [app impostazioni Apple Watch](~/ios/watchos/app-fundamentals/settings.md) nel simulatore iPhone oppure generando una [notifica](~/ios/watchos/platform/notifications.md) e confermando che l'icona viene visualizzata nella schermata di notifica.

> [!NOTE]
> Le icone non possono avere un canale alfa (l'app verrà rifiutata durante l'invio dell'app Store se è presente un canale alfa). È possibile verificare se esiste un canale alfa e rimuoverlo [usando l'app di anteprima in Mac OS X](~/ios/watchos/troubleshooting.md#noalpha).

## <a name="related-links"></a>Collegamenti correlati

- [Icona di Apple watchos & Guida alle immagini](https://developer.apple.com/design/human-interface-guidelines/watchos/icons-and-images/)
