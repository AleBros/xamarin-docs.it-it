---
title: Uso delle dimensioni dello schermo watchos in Novell
description: Questo documento descrive come usare diverse dimensioni dello schermo watchos. Viene illustrata la finestra di progettazione dell'interfaccia watchos, il simulatore watchos e le risorse immagine.
ms.prod: xamarin
ms.assetid: 840DF939-2F59-4ABA-87D8-92AAC8A92BC4
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 03/17/2017
ms.openlocfilehash: e9c87b76dc6845962450b8cb6fab921ea1748832
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/06/2019
ms.locfileid: "70768321"
---
# <a name="working-with-watchos-screen-sizes-in-xamarin"></a>Uso delle dimensioni dello schermo watchos in Novell

Apple Watch è disponibile in due dimensioni dello schermo:

- **38mm**
  - 136 x 170 pixel logici (272 x 340 pixel fisici)

- **42mm**
  - 156 x 195 pixel logici (312 x 390 pixel fisici).

Quando si progettano e si testano le app, è necessario tenere conto delle dimensioni dello schermo.

## <a name="watchos-interface-designer"></a>Progettazione interfacce watchos

Per impostazione predefinita, nella finestra di progettazione Visual Studio per Mac vengono visualizzati i controller di interfaccia di controllo in **qualsiasi Apple Watch**.

![](screen-sizes-images/screen-any-sml.png "Nella finestra di progettazione vengono visualizzati i controller di interfaccia di controllo in qualsiasi Apple Watch")

Usare il menu dimensioni per modificare e visualizzare in anteprima lo storyboard in una delle dimensioni dello schermo disponibili: **38** mm o **42**mm:

![](screen-sizes-images/screen-menu-sml.png "Selezione delle dimensioni di 38 mm o 42 mm")

Le dimensioni dello schermo più grandi a volte comporteranno il rendering del contenuto che verrebbe troncato/nascosto nella schermata più piccola.
Assicurarsi di eseguire il test su entrambe le dimensioni.

### <a name="interface-design"></a>Progettazione di interfacce

L'app deve visualizzare lo stesso contenuto sullo schermo, indipendentemente dalle dimensioni, e deve espandere o comprimere gli elementi in base alle esigenze. Nella finestra di progettazione di Visual Studio per Mac, nel controllo attributi, è necessario utilizzare **relativo a contenitore** o **dimensione per adattare il contenuto** in base alle dimensioni fisse.

![](screen-sizes-images/sizeattributepanel-sml.png "Usare relativo a un contenitore o a una dimensione per adattare il contenuto in base alle dimensioni fisse")

Poiché la schermata di controllo è racchiusa da una lunetta nera, non è consigliabile fornire spaziatura intorno all'interfaccia. Lasciare gli elementi inattivi rispetto al bordo dello schermo e lasciare che la lunetta formi un bordo naturale intorno all'app.

## <a name="watchos-simulator"></a>Simulatore watchos

Quando si esegue il test sul simulatore, è possibile passare facilmente tra le due dimensioni dello schermo usando il menu **Hardware > dispositivo** .

![](screen-sizes-images/simulator.png "Il simulatore può spostarsi tra le due dimensioni dello schermo usando il menu dispositivo hardware")

## <a name="image-resources"></a>Risorse immagine

È consigliabile usare più asset di immagine se un singolo asset non ha un aspetto corretto a dimensioni diverse. I cataloghi asset di immagini consentono di specificare bitmap separate per ogni dimensione:

![](screen-sizes-images/images-xcassets.png "Editor del catalogo asset immagini")

```csharp
// specify the asset name, the correct size will automatically be loaded
staticImage.SetImage(UIImage.FromBundle("Walkway"));
```

In alternativa, usare il codice per determinare le dimensioni dello schermo e caricare completamente immagini diverse:

```csharp
bool large = WKInterfaceDevice.CurrentDevice.ScreenBounds.Size.Width > 136.0;
// Load image depending on screen size
using (var image = UIImage.FromBundle (large ? "42mm-Walkway" : "38mm-Walkway"))
{
   myImage.SetImage (image);

}
```

Altre informazioni sull'uso del [controllo immagine](~/ios/watchos/user-interface/image.md).

## <a name="related-links"></a>Collegamenti correlati

- [Introduzione a watchOS 3](~/ios/watchos/platform/introduction-to-watchos3/index.md)
