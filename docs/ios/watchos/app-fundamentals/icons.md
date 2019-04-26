---
title: Utilizzo di watchOS icone in Xamarin
description: Questo documento descrive le varie icone necessarie per un'applicazione watchOS e su come configurare una soluzione per includere tali icone.
ms.prod: xamarin
ms.assetid: EE3D45BD-8091-4C04-BA83-371371D8BEB9
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 07/26/2018
ms.openlocfilehash: 435af10484827826d53b767c2738e3945e0bae42
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61345240"
---
# <a name="working-with-watchos-icons-in-xamarin"></a>Utilizzo di watchOS icone in Xamarin

Apple Watch soluzioni richiedono due set di icone:

* Le icone dell'app iOS che verranno visualizzato nell'iPhone.
* Icone di Apple Watch che verranno sottoposti a rendering in un cerchio nel menu di espressioni di controllo e nelle schermate di notifica. Icona dell'app watch viene visualizzata anche nella [Apple Watch](~/ios/watchos/app-fundamentals/settings.md) app iOS.

## <a name="apple-watch-icons"></a>Icone di Apple Watch

| | | |
|-|-|-|
|Icona dell'App iOS|Viene visualizzata su iPhone e avvia l'app padre|![icona dell'app iOS](icons-images/icon-ios.png)|
|Icona dell'App Watch|Viene visualizzata nella schermata iniziale di Apple Watch|![icona dell'app watchOS](icons-images/icon-home.png)|
||Viene visualizzato nelle notifiche di espressioni di controllo|![icona di notifica di watchOS](icons-images/notification-icon.png)|
||Viene visualizzato nei [iOS App Apple Watch](~/ios/watchos/app-fundamentals/settings.md)|![icona iOS App Watch](icons-images/watch-app-sml.png)|

## <a name="configuring-your-solution"></a>Configurazione della soluzione

Per assicurarsi che l'app per iOS e app watch Mostra un'icona e il nome corretto, seguire queste istruzioni per ogni progetto:

### <a name="ios-app"></a>App iOS

Fare riferimento al [Guida per le icone dell'applicazione iOS](~/ios/app-fundamentals/images-icons/app-icons.md) per assicurarsi che siano configurate correttamente le icone dell'app iOS.

#### <a name="infoplist"></a>Info. plist

La stringa visualizzata accanto all'app watch nel [Apple Watch impostazioni app](~/ios/watchos/app-fundamentals/settings.md) configurato nel **Info. plist dell'app iOS**.

Verificare che il **Info. plist** ha una `CFBundleName` chiave e il valore (Nota: questo comportamento è diverso per il `CFBundleDisplayName`, è possibile avere sia):

```xml
<key>CFBundleName</key>
<string>Your App Name</string>
```

### <a name="apple-watch-app"></a>Apple Watch App

Una volta il [app padre](~/ios/watchos/app-fundamentals/parent-app.md) ha configurato le icone, è necessario aggiungere un catalogo di asset di icona dell'applicazione all'app watch.

1. Pulsante destro del mouse sul progetto di App di espressioni di controllo e selezionare **File > Aggiungi > Nuovo File... > iOS > catalogo di Asset** per aggiungere un catalogo asset per il progetto.

 ![](icons-images/newasset.png "Aggiungere un catalogo asset per il progetto")

2. Fare doppio clic sulla **AppIcon.appiconset/Contents.json** file

  ![](icons-images/xcassets-iconset-sml.png "Il contenuto AppIcon")

3. Aggiungere tutte le immagini di watchOS, come illustrato in questo screenshot:

  [![](icons-images/appicons-sml.png "Aggiungere tutte le immagini di watchOS, come illustrato in questo screenshot")](icons-images/appicons.png#lightbox)

  Fare riferimento a [linee guida sull'icona di Apple](https://developer.apple.com/design/human-interface-guidelines/watchos/icons-and-images/menu-icons/) per le dimensioni richieste (le dimensioni vengono anche visualizzate sullo schermo). Tenere presente che queste icone verranno ritagliate automaticamente per il rendering in un cerchio.

  L'elenco di icone dovrebbe essere simile al seguente:

  ![](icons-images/xcassets-complete-sml.png "L'elenco di icone in Esplora soluzioni")

4. Per garantire il catalogo di asset è incluso nell'app, aggiungere la seguente chiave e il valore per il **Info. plist dell'App Watch**:

```xml
<key>XSAppIconAssets</key>
<string>Images.xcassets/AppIcon.appiconset</string>
```

È possibile verificare le icone vengono configurate corretto controllando le [Apple Watch impostazioni app](~/ios/watchos/app-fundamentals/settings.md) nel simulatore di iPhone o la generazione di un [notifica](~/ios/watchos/platform/notifications.md) e conferma l'icona viene visualizzata la notifica schermata.

> [!NOTE]
> Le icone non possono avere un canale alfa (l'app verrà rifiutata durante l'invio di App Store se è presente un canale alfa). È possibile verificare che esista un canale alfa e rimuoverlo [usando l'app di anteprima in Mac OS X](~/ios/watchos/troubleshooting.md#noalpha).


## <a name="related-links"></a>Collegamenti correlati

- [Guida di immagini e icone di watchOS di Apple](https://developer.apple.com/design/human-interface-guidelines/watchos/icons-and-images/)
