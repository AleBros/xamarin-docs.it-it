---
title: Uso delle dimensioni dello schermo watchos in Novell
description: Questo documento descrive come usare diverse dimensioni dello schermo watchos. Viene illustrata la finestra di progettazione dell'interfaccia watchos, il simulatore watchos e le risorse immagine.
ms.prod: xamarin
ms.assetid: 840DF939-2F59-4ABA-87D8-92AAC8A92BC4
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/17/2017
ms.openlocfilehash: aeaa1bb1273bc062e0ac76eaa09722827f15797f
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73028401"
---
# <a name="working-with-watchos-screen-sizes-in-xamarin"></a>Uso delle dimensioni dello schermo watchos in Novell

Apple Watch è disponibile in due dimensioni dello schermo:

- **38mm**
  - 136 x 170 pixel logici (272 x 340 pixel fisici)

- **da 42mm**
  - 156 x 195 pixel logici (312 x 390 pixel fisici).

Quando si progettano e si testano le app, è necessario tenere conto delle dimensioni dello schermo.

## <a name="watchos-interface-designer"></a>Progettazione interfacce watchos

Per impostazione predefinita, nella finestra di progettazione Visual Studio per Mac vengono visualizzati i controller di interfaccia di controllo in **qualsiasi Apple Watch**.

![](screen-sizes-images/screen-any-sml.png "The Designer displays watch interface controllers at Any Apple Watch")

Usare il menu dimensioni per modificare e visualizzare in anteprima lo storyboard in una delle dimensioni dello schermo disponibili: **38** mm o **42**mm:

![](screen-sizes-images/screen-menu-sml.png "Selecting the 38mm or 42mm size")

Le dimensioni dello schermo più grandi a volte comporteranno il rendering del contenuto che verrebbe troncato/nascosto nella schermata più piccola.
Assicurarsi di eseguire il test su entrambe le dimensioni.

### <a name="interface-design"></a>Progettazione di interfacce

L'app deve visualizzare lo stesso contenuto sullo schermo, indipendentemente dalle dimensioni, e deve espandere o comprimere gli elementi in base alle esigenze. Nella finestra di progettazione di Visual Studio per Mac, nel controllo attributi, è necessario utilizzare **relativo a contenitore** o **dimensione per adattare il contenuto** in base alle dimensioni fisse.

![](screen-sizes-images/sizeattributepanel-sml.png "Use Relative to Container or Size to Fit Content in preference to fixed sizes")

Poiché la schermata di controllo è racchiusa da una lunetta nera, non è consigliabile fornire spaziatura intorno all'interfaccia. Lasciare gli elementi inattivi rispetto al bordo dello schermo e lasciare che la lunetta formi un bordo naturale intorno all'app.

## <a name="watchos-simulator"></a>Simulatore watchos

Quando si esegue il test sul simulatore, è possibile passare facilmente tra le due dimensioni dello schermo usando il menu **Hardware > dispositivo** .

![](screen-sizes-images/simulator.png "The simulator can switch between the two screen sizes using the Hardware Device menu")

## <a name="image-resources"></a>Risorse immagine

È consigliabile usare più asset di immagine se un singolo asset non ha un aspetto corretto a dimensioni diverse. I cataloghi asset di immagini consentono di specificare bitmap separate per ogni dimensione:

![](screen-sizes-images/images-xcassets.png "Image asset catalog editor")

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
