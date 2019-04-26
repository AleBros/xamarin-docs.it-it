---
title: Utilizzo di watchOS le dimensioni dello schermo in Xamarin
description: Questo documento descrive come usare diverse dimensioni dello schermo watchOS. Illustra la progettazione di interfaccia, watchOS simulatore, watchOS e risorse immagine.
ms.prod: xamarin
ms.assetid: 840DF939-2F59-4ABA-87D8-92AAC8A92BC4
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/17/2017
ms.openlocfilehash: b2f4cc71c1993e51ed55b51edd7c50d393e60873
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61412911"
---
# <a name="working-with-watchos-screen-sizes-in-xamarin"></a>Utilizzo di watchOS le dimensioni dello schermo in Xamarin

Apple Watch è disponibile in due dimensioni dello schermo:

- **38mm**
  - 136 x 170 pixel logici (pixel fisici 272 x 340)

- **42mm**
  - 156 x 195 pixel logici (pixel fisici 312 x 390).

È necessario tenere conto durante la progettazione e test di App di dimensioni dello schermo.

## <a name="watchos-interface-designer"></a>finestra di progettazione di interfaccia watchOS

Per impostazione predefinita di Visual Studio per Mac progettazione verranno visualizzati i controller di interfaccia in guardare **Apple Watch Any**.

![](screen-sizes-images/screen-any-sml.png "Consente di visualizzare la finestra di progettazione guardare i controller di interfaccia in Apple Watch Any")

Usare il menu di dimensioni per modificare e visualizzare in anteprima lo storyboard a una delle dimensioni della schermata disponibili: **38mm** oppure **42mm**:

![](screen-sizes-images/screen-menu-sml.png "Selezionando le sue dimensioni 38mm o 42mm")

Dimensioni dello schermo più grande in alcuni casi verranno eseguito il rendering di contenuto che verrebbero troncati o nascosta su schermi più piccoli.
Assicurarsi di testare in entrambe le dimensioni.


### <a name="interface-design"></a>Progettazione di interfacce

L'app deve visualizzare lo stesso contenuto sullo schermo, indipendentemente dalle dimensioni e deve espandere o comprimere gli elementi di base alle esigenze. In Visual Studio per Mac progettazione, in Attribute Inspector è consigliabile utilizzare **relativo al contenitore** oppure **dimensioni per adattarsi contenuto** preferenza rispetto a dimensioni fisse.

![](screen-sizes-images/sizeattributepanel-sml.png "Usare Relative al contenitore o dimensioni per adattarsi contenuto preferenza rispetto a dimensioni fisse")

Poiché la schermata di espressioni di controllo è delimitata da un nero, fornendo la spaziatura interna intorno all'interfaccia non è consigliato. Consentire gli elementi inattivi contro il bordo dello schermo e consentire la cornice formano un naturale bordo intorno all'app.


## <a name="watchos-simulator"></a>Simulatore di watchOS

Quando testing sul simulatore è possibile passare facilmente tra le dimensioni dello due schermo usando il **Hardware > dispositivo** menu.

![](screen-sizes-images/simulator.png "Il simulatore può passare tra le dimensioni dello due schermo usando il menu di dispositivo Hardware")


## <a name="image-resources"></a>Risorse immagine

Se non ha l'aspetto efficaci per diverse dimensioni di un singolo asset, è consigliabile usare più asset di immagine. Cataloghi asset immagine consentono la bitmap separata per ogni dimensione di specificare:

![](screen-sizes-images/images-xcassets.png "Editor del catalogo asset immagine")

```csharp
// specify the asset name, the correct size will automatically be loaded
staticImage.SetImage(UIImage.FromBundle("Walkway"));
```

In alternativa, usare codice per determinare le dimensioni dello schermo e caricare immagini diverse del tutto:

```csharp
bool large = WKInterfaceDevice.CurrentDevice.ScreenBounds.Size.Width > 136.0;
// Load image depending on screen size
using (var image = UIImage.FromBundle (large ? "42mm-Walkway" : "38mm-Walkway"))
{
   myImage.SetImage (image);

}
```

Altre informazioni su usando il [controllo immagine](~/ios/watchos/user-interface/image.md).



## <a name="related-links"></a>Collegamenti correlati

- [Introduzione a watchOS 3](~/ios/watchos/platform/introduction-to-watchos3/index.md)
