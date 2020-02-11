---
title: >
  Uso delle icone watchos in Xamarin
description: Questo documento descrive le varie icone necessarie per un'applicazione watchos e come configurare una soluzione per includere queste icone.
ms.prod: xamarin
ms.assetid: EE3D45BD-8091-4C04-BA83-371371D8BEB9
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 07/26/2018
ms.openlocfilehash: c8c5b8d0417fb7fd1069d2bf6fa5d9887d569453
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73001566"
---
# <a name="working-with-watchos-icons-in-xamarin"></a>Uso delle icone watchos in Xamarin


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

Verificare che il file **info. plist** disponga di una chiave e di un valore di `CFBundleName` (Nota: questa operazione è diversa da quella del `CFBundleDisplayName`, è possibile avere entrambe):

```xml
<key>CFBundleName</key>
<string>Your App Name</string>
```

### <a name="apple-watch-app"></a>App Apple Watch

Una volta configurate le icone per l' [app padre](~/ios/watchos/app-fundamentals/parent-app.md) , è necessario aggiungere un'icona dell'applicazione Catalogo asset all'app Watch.

1. Fare clic con il pulsante destro del mouse sul progetto Watch app e selezionare **file > aggiungi > nuovo file... > il catalogo asset > iOS** per aggiungere un catalogo asset al progetto.

    ![](icons-images/newasset.png "Add an asset catalog to the project")

2. Fare doppio clic sul file **AppIcon. appiconset/Contents. JSON**

    ![](icons-images/xcassets-iconset-sml.png "The AppIcon contents")

3. Aggiungere tutte le immagini watchos, come illustrato in questo screenshot:

    [![](icons-images/appicons-sml.png "Add all the watchOS images, as shown in this screenshot")](icons-images/appicons.png#lightbox)

    Vedere le [linee guida sulle icone di Apple](https://developer.apple.com/design/human-interface-guidelines/watchos/icons-and-images/menu-icons/) per le dimensioni richieste (le dimensioni sono visualizzate anche sullo schermo). Tenere presente che queste icone verranno ritagliate automaticamente per il rendering in un cerchio.

    L'elenco di icone dovrebbe avere un aspetto simile al seguente:

    ![](icons-images/xcassets-complete-sml.png "The icon list in the Solution Explorer")

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
