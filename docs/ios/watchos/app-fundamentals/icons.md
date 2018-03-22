---
title: Utilizzo di icone
ms.topic: article
ms.prod: xamarin
ms.assetid: EE3D45BD-8091-4C04-BA83-371371D8BEB9
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.openlocfilehash: 2961eb4726b9f313d01f8bc075e5ca362d708e92
ms.sourcegitcommit: 73bd0c7e5f237f0a1be70a6c1384309bb26609d5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/22/2018
---
# <a name="working-with-icons"></a>Utilizzo di icone

Soluzioni di Apple Watch richiedono due set di icone:

* Le icone dell'app iOS che verranno visualizzato su iPhone.
* Icone di Apple Watch che verranno sottoposti a rendering in un cerchio dal menu di espressioni di controllo e nelle schermate di notifica. L'icona di app di espressioni di controllo viene visualizzata anche la [Apple Watch](~/ios/watchos/app-fundamentals/settings.md) app iOS.

## <a name="apple-watch-icons"></a>Icone di Apple Watch

| | | |
|-|-|-|
|Icona App iOS|Verrà visualizzata sull'iPhone e avvia l'app padre|![](icons-images/icon-ios.png)|
|Icona App|Viene visualizzata nella schermata iniziale di Apple Watch|![](icons-images/icon-home.png)|
||Viene visualizzato nelle notifiche di espressioni di controllo|![](icons-images/notification-icon.png)|
||È presente il [Apple Watch App iOS](~/ios/watchos/app-fundamentals/settings.md)|![](icons-images/watch-app-sml.png)|

## <a name="configuring-your-solution"></a>Configurazione di soluzione

Per garantire che l'app iOS e app di espressioni di controllo Mostra il nome corretto e l'icona, seguire queste istruzioni per ogni progetto:

### <a name="ios-app"></a>iOS App

Consultare il [icone applicazioni iOS Guida](~/ios/app-fundamentals/images-icons/app-icons.md) affinché siano configurate correttamente le icone dell'app iOS.

#### <a name="infoplist"></a>Info. plist

La stringa visualizzata accanto all'app di espressioni di controllo nella [Apple Watch impostazioni app](~/ios/watchos/app-fundamentals/settings.md) è configurato nel **Info. plist dell'app iOS**.

Verificare che il **Info. plist** ha un `CFBundleName` chiave e il valore (Nota: questo comportamento è diverso per il `CFBundleDisplayName`, è possibile disporre di entrambi):

```xml
<key>CFBundleName</key>
<string>Your App Name</string>
```

### <a name="apple-watch-app"></a>App di Apple Watch

Una volta il [app padre](~/ios/watchos/app-fundamentals/parent-app.md) ha configurato le icone, è necessario aggiungere un catalogo di asset icona applicazione per l'applicazione di espressioni di controllo.

1. Fare clic sul progetto di applicazione delle espressioni di controllo e selezionare **File > Aggiungi > Nuovo File... > iOS > catalogo di Asset** per aggiungere un catalogo di asset per il progetto.

 ![](icons-images/newasset.png "Aggiungere un catalogo di asset al progetto")

2. Fare doppio clic su di **AppIcons.appiconset/Contents.json** file

  ![](icons-images/xcassets-iconset-sml.png "Il contenuto di AppIcons")

3. Aggiungere tutte le immagini watchOS, come illustrato in questo screenshot:

  [![](icons-images/appicons-sml.png "Aggiungere tutte le immagini watchOS, come illustrato in questa schermata")](icons-images/appicons.png#lightbox)

  Fare riferimento a [linee guida sull'icona di Apple](https://developer.apple.com/library/prerelease/ios/documentation/UserExperience/Conceptual/WatchHumanInterfaceGuidelines/IconandImageSizes.html) per le dimensioni necessarie (le dimensioni vengono visualizzate anche nella schermata). Tenere presente che queste icone verranno troncate automaticamente per il rendering in un cerchio.

  L'elenco di icona dovrebbe essere simile al seguente:

  ![](icons-images/xcassets-complete-sml.png "L'elenco di icone in Esplora soluzioni")

4. Per garantire il catalogo di asset è incluso nell'app, aggiungere la seguente chiave e il valore per il **Info. plist dell'App Watch**:

```xml
<key>XSAppIconAssets</key>
<string>Images.xcassets/AppIcons.appiconset</string>
```

È possibile verificare le icone vengono configurate corretto controllando il [Apple Watch impostazioni app](~/ios/watchos/app-fundamentals/settings.md) il simulatore di iPhone o generando un [notifica](~/ios/watchos/platform/notifications.md) e conferma l'icona verrà visualizzata la notifica schermata.

> [!NOTE]
> Le icone non possono avere un canale alfa (l'app verrà rifiutato durante l'invio allo Store di App se è presente un canale alfa). È possibile verificare se un canale alfa di rimuoverlo [tramite l'app di anteprima in Mac OS X](~/ios/watchos/troubleshooting.md#noalpha).


## <a name="related-links"></a>Collegamenti correlati

- [Icona di Apple e immagini della Guida](https://developer.apple.com/library/prerelease/ios/documentation/UserExperience/Conceptual/WatchHumanInterfaceGuidelines/IconandImageSizes.html)
