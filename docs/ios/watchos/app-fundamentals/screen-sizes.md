---
title: Utilizzo delle dimensioni dello schermo
ms.topic: article
ms.prod: xamarin
ms.assetid: 840DF939-2F59-4ABA-87D8-92AAC8A92BC4
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/17/2017
ms.openlocfilehash: 81e0a9c8d65e354704f01ce70b8eb0b1b67f993f
ms.sourcegitcommit: 0fdb243b46cf21be47584900805cadcd077121bf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/12/2018
---
# <a name="working-with-screen-sizes"></a>Utilizzo delle dimensioni dello schermo

Apple Watch è disponibile in due dimensioni:

- **38mm**
  - 136 x 170 pixel logici (pixel fisici 272 x 340)

- **42mm**
  - 156 x 195 pixel logici (pixel fisici 312 x 390).

Dimensioni dello schermo tenere in considerazione durante la progettazione e il test delle applicazioni.

## <a name="watchos-interface-designer"></a>finestra di progettazione dell'interfaccia watchOS

Per impostazione predefinita di Visual Studio per Mac progettazione visualizzerà guardare i controller di interfaccia **Any Apple Watch**.

![](screen-sizes-images/screen-any-sml.png "Consente di visualizzare la finestra di progettazione guardare i controller di interfaccia in qualsiasi Apple Watch")

Utilizzare il menu di dimensioni per modificare e visualizzare in anteprima lo storyboard in una delle dimensioni dello schermo disponibile: **38mm** o **42mm**:

![](screen-sizes-images/screen-menu-sml.png "Selezionare la dimensione 38 o mm 42")

Le dimensioni dello schermo più grande in alcuni casi verranno eseguito il rendering di contenuto che potrebbe essere troncato o nascosta sullo schermo più piccolo.
Assicurarsi di eseguire i test su entrambe le dimensioni.


### <a name="interface-design"></a>Progettazione di interfacce

L'app deve visualizzare il contenuto stesso sullo schermo, indipendentemente dalle dimensioni e deve espandere o comprimere gli elementi di base alle esigenze. In Visual Studio per Mac progettazione del controllo di attributo, è necessario utilizzare **relativo al contenitore** o **dimensioni Fit Content** a dimensioni fisse.

![](screen-sizes-images/sizeattributepanel-sml.png "Utilizzare relativa al contenitore o le dimensioni di base del contenuto a dimensioni fisse")

Poiché la schermata di espressioni di controllo è delimitata da un riquadro nero, non è consigliabile fornire spaziatura intorno a un'interfaccia. Gli elementi in modo rest contro il bordo dello schermo e consentono la cornice di formare un bordo intorno all'app naturale.


## <a name="watchos-simulator"></a>watchOS simulatore

Quando si verifica nel simulatore passare facilmente tra le dimensioni dello due schermo utilizzando il **Hardware > dispositivo** menu.

![](screen-sizes-images/simulator.png "Il simulatore può passare tra le dimensioni dello due schermo utilizzando il menu di dispositivo Hardware")


## <a name="image-resources"></a>Risorse immagini

Se un singolo asset non visualizzate correttamente a dimensioni diverse, è consigliabile utilizzare più asset di immagine. Cataloghi di asset immagine consentono di bitmap separata per ogni dimensione:

![](screen-sizes-images/images-xcassets.png "Editor del catalogo di asset immagine")

```csharp
// specify the asset name, the correct size will automatically be loaded
staticImage.SetImage(UIImage.FromBundle("Walkway"));
```

In alternativa, è possibile utilizzare codice per determinare le dimensioni dello schermo e caricare immagini differenti completamente:

```csharp
bool large = WKInterfaceDevice.CurrentDevice.ScreenBounds.Size.Width > 136.0;
// Load image depending on screen size
using (var image = UIImage.FromBundle (large ? "42mm-Walkway" : "38mm-Walkway"))
{
   myImage.SetImage (image);

}
```

Lettura ulteriori informazioni sull'utilizzo di [controllo immagine](~/ios/watchos/user-interface/image.md).



## <a name="related-links"></a>Collegamenti correlati

- [Introduzione a watchOS 3](~/ios/watchos/platform/introduction-to-watchos3/index.md)
