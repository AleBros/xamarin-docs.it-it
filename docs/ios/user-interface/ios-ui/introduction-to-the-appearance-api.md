---
title: Aspetto API
description: "iOS consente di applicare le impostazioni di proprietà visiva a un livello di classe statica anziché su singoli oggetti, in modo che la modifica si applica a tutte le istanze di tale controllo nell'applicazione."
ms.topic: article
ms.prod: xamarin
ms.assetid: C1727F0C-82B1-D085-D46F-C6383FF04B16
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/22/2017
ms.openlocfilehash: f35256529d6d72a3f5e563dc88b9d5883a9724d4
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/09/2018
---
# <a name="appearance-api"></a>Aspetto API

_iOS consente di applicare le impostazioni di proprietà visiva a un livello di classe statica anziché su singoli oggetti, in modo che la modifica si applica a tutte le istanze di tale controllo nell'applicazione._

Questa funzionalità è esposta in xamarin. IOS tramite un valore statico `Appearance` proprietà su tutti i controlli UIKit che la supportano. Aspetto visivo (proprietà come come immagine di sfondo e di colore tinta) pertanto facilmente personalizzabile per fornire all'applicazione un aspetto coerente. L'API di aspetto è stata introdotta in iOS 5 e sebbene alcune parti di esso sono state deprecate in iOS 9 è ancora un buon metodo per ottenere alcuni stili e gli effetti dei temi in App xamarin. IOS.

## <a name="overview"></a>Panoramica

iOS consente che personalizzare l'aspetto di molti controlli UIKit per visualizzare i controlli standard di conformità per la personalizzazione che si desidera utilizzare per l'applicazione.

Esistono due diversi modi per applicare un aspetto personalizzato:

- **Direttamente in un'istanza del controllo** : è possibile impostare il colore tonalità, immagine di sfondo e posizione del titolo (nonché gli altri attributi) su molti controlli, incluse le barre degli strumenti, barre di navigazione, pulsanti e dispositivi di scorrimento.

- **Impostare i valori predefiniti per la proprietà statica aspetto** : gli attributi personalizzabili per ogni controllo sono esposte tramite la `Appearance` proprietà statica. Tutte le personalizzazioni applicate a queste proprietà da utilizzare come valore predefinito per qualsiasi controllo dello stesso tipo che verrà creato quando la proprietà è impostata.

L'applicazione di esempio aspetto illustra tutte e tre i metodi, come illustrato in queste schermate:

 [![](introduction-to-the-appearance-api-images/appearance01.png "L'applicazione di esempio aspetto illustra tutte e tre i metodi")](introduction-to-the-appearance-api-images/appearance01.png#lightbox)

A partire da iOS 8, il proxy di aspetto è stato esteso per TraitCollections.
 `AppearanceForTraitCollection` Consente di impostare l'aspetto predefinito per un insieme specifico di tratto. È possibile leggere altre informazioni di [Introduzione agli storyboard](~/ios/user-interface/storyboards/unified-storyboards.md) Guida.


## <a name="setting-appearance-properties"></a>Impostazione delle proprietà di aspetto

Nella prima schermata, la classe aspetto statica viene utilizzata per definire lo stile di pulsanti e gli elementi di giallo/arancione simile al seguente:

```csharp
// Set the default appearance values
UIButton.Appearance.TintColor = UIColor.LightGray;
UIButton.Appearance.SetTitleColor(UIColor.FromRGB(0,127,14), UIControlState.Normal);

UISlider.Appearance.ThumbTintColor = UIColor.Red;
UISlider.Appearance.MinimumTrackTintColor = UIColor.Orange;
UISlider.Appearance.MaximumTrackTintColor = UIColor.Yellow;

UIProgressView.Appearance.ProgressTintColor = UIColor.Yellow;
UIProgressView.Appearance.TrackTintColor = UIColor.Orange;
```

Gli stili di colore verde elemento sono impostati in questo tipo, il `ViewDidLoad` metodo che sostituisce i valori predefiniti e *aspetto* classe statica:

```csharp
slider2.ThumbTintColor = UIColor.FromRGB (0,127,70); // dark green
slider2.MinimumTrackTintColor = UIColor.FromRGB (66,255,63);
slider2.MaximumTrackTintColor = UIColor.FromRGB (197,255,132);
```

```csharp
progress2.ProgressTintColor = UIColor.FromRGB (66,255,63);
progress2.TrackTintColor = UIColor.FromRGB (197,255,132);
```

## <a name="using-uiappearance-in-xamarinforms"></a>Utilizzo di UIAppearance in xamarin. Forms

L'API di aspetto può essere utile quando [stile l'app iOS](~/xamarin-forms/platform/ios/theme.md#uiappearance) nelle soluzioni xamarin. Forms. Alcune righe di `AppDelegate` classe consentono di implementare una combinazione di colori specifici senza dover creare un [renderer personalizzato](~/xamarin-forms/app-fundamentals/custom-renderer/index.md).


### <a name="custom-themes-and-uiappearance"></a>UIAppearance e temi personalizzati

iOS consente molti attributi visivi dell'utente ai controlli di interfaccia di "tema" utilizzando il *UIAppearance* API per forzare tutte le istanze di un particolare controllo hanno lo stesso aspetto. Vengono esposte come proprietà di aspetto, molte classi di controllo dell'interfaccia utente e non in singole istanze del controllo. L'impostazione di una proprietà di visualizzazione in statica `Appearance` proprietà ha effetto su tutti i controlli di quel tipo nell'applicazione.

Per comprendere meglio il concetto, si consideri un esempio.

Per modificare un oggetto specifico `UISegmentedControl` per una tonalità di colore Magenta, si fa riferimento il controllo specifico nella nostra schermata simile alla seguente in `ViewDidLoad`:

```csharp
sg1.TintColor = UIColor.Magenta;
```

In alternativa, è possibile impostare il valore nel riquadro proprietà della finestra di progettazione: 

[![](introduction-to-the-appearance-api-images/propertiespadtint.png "Proprietà riempimento Tint")](introduction-to-the-appearance-api-images/propertiespadtint.png#lightbox)

Nell'immagine seguente viene illustrato che consente di impostare la tonalità sul controllo denominato 'sg1'.

 [![](introduction-to-the-appearance-api-images/image53.png "Impostare la tonalità singolo controllo")](introduction-to-the-appearance-api-images/image53.png#lightbox)

Per impostare molti controlli in questo modo sarebbe completamente inefficiente, in modo che è invece possibile impostare statica `Appearance` proprietà sulla classe stessa. Come illustrato nel codice seguente:

```csharp
UISegmentedControl.Appearance.TintColor = UIColor.Magenta;
```

Nell'immagine seguente illustra ora entrambi i controlli segmentati con l'aspetto impostato su Magenta:

 [![](introduction-to-the-appearance-api-images/image54.png "Impostare la tonalità di controllo di aspetto")](introduction-to-the-appearance-api-images/image54.png#lightbox)

`Appearance` proprietà devono essere impostate nelle prime fasi del ciclo di vita dell'applicazione, ad esempio nel AppDelegate `FinishedLaunching` evento, o in un ViewController prima che vengano visualizzati i controlli interessati.


Consultare la [Introduzione all'API di aspetto](~/ios/user-interface/ios-ui/introduction-to-the-appearance-api.md) per informazioni più dettagliate.


## <a name="related-links"></a>Collegamenti correlati

- [Aspetto (esempio)](https://developer.xamarin.com/samples/monotouch/IntroToAppearance/)
- [Riferimento al protocollo UIAppearance](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UIAppearance_Protocol/)
- [Aspetto in xamarin. Forms](~/xamarin-forms/platform/ios/theme.md#uiappearance)
