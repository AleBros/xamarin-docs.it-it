---
title: API Appearance in xamarin. IOS
description: iOS consente di applicare le impostazioni di proprietà di visualizzazione a un livello di classe statica anziché su singoli oggetti in modo che la modifica viene applicata a tutte le istanze di tale controllo nell'applicazione.
ms.prod: xamarin
ms.assetid: C1727F0C-82B1-D085-D46F-C6383FF04B16
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 11/15/2018
ms.openlocfilehash: 86055932edf82f04e11c9ae8dd684a3d77c3d830
ms.sourcegitcommit: da2260a882dbda02732e21cad5cc9a9dd35e27e7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/15/2018
ms.locfileid: "51709228"
---
# <a name="appearance-api-in-xamarinios"></a>API Appearance in xamarin. IOS

_iOS consente di applicare le impostazioni di proprietà di visualizzazione a un livello di classe statica anziché su singoli oggetti in modo che la modifica viene applicata a tutte le istanze di tale controllo nell'applicazione._

Questa funzionalità è esposta in xamarin. IOS tramite un valore statico `Appearance` proprietà in tutti i controlli UIKit che lo supportano. Aspetto visivo (proprietà, ad esempio come immagine di colore e sfondo tinta) può pertanto essere personalizzato facilmente per consentire all'applicazione un aspetto coerente. L'API di aspetto è stato introdotto in iOS 5 e anche se sono state deprecate alcune parti di esso in iOS 9 è ancora un buon metodo per ottenere alcuni stili e gli effetti dei temi in App xamarin. IOS.

## <a name="overview"></a>Panoramica

iOS consente che personalizzare l'aspetto di molti controlli UIKit per rendere i controlli standard conformi per le informazioni di personalizzazione da applicare all'applicazione.

Esistono due diversi modi per applicare un aspetto personalizzato:

- **Direttamente in un'istanza del controllo** – è possibile impostare il colore tonalità, immagine di sfondo e posizione del titolo (nonché altri attributi) su molti controlli, tra cui le barre degli strumenti, barre di navigazione, pulsanti e i dispositivi di scorrimento.

- **Impostare i valori predefiniti per la proprietà statica aspetto** – gli attributi personalizzabili per ogni controllo vengono esposte tramite il `Appearance` proprietà statica. Le eventuali personalizzazioni che applicano a queste proprietà da utilizzare come valore predefinito per qualsiasi controllo dello stesso tipo che viene creato dopo aver impostata la proprietà.

L'applicazione di esempio di aspetto illustra tutti i tre metodi, come illustrato nelle schermate illustrate:

[![](introduction-to-the-appearance-api-images/appearance01-sml.png "L'applicazione di esempio di aspetto illustra tutti tre e")](introduction-to-the-appearance-api-images/appearance01.png#lightbox)

A partire da iOS 8, il proxy di aspetto è stato esteso alle TraitCollections.
 `AppearanceForTraitCollection` Consente di impostare l'aspetto predefinito in una raccolta di tratto particolare. È possibile leggere altre informazioni, vedere la [Introduzione agli storyboard](~/ios/user-interface/storyboards/unified-storyboards.md) Guida.

## <a name="setting-appearance-properties"></a>L'impostazione di proprietà dell'aspetto

Nella prima schermata, la classe statica di aspetto viene usata per definire lo stile i pulsanti e gli elementi di giallo/arancione simile al seguente:

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

Gli stili di colore verde elemento vengono impostati in simile al seguente, il `ViewDidLoad` metodo che sostituisce i valori predefiniti e la *aspetto* classe statica:

```csharp
slider2.ThumbTintColor = UIColor.FromRGB (0,127,70); // dark green
slider2.MinimumTrackTintColor = UIColor.FromRGB (66,255,63);
slider2.MaximumTrackTintColor = UIColor.FromRGB (197,255,132);
```

```csharp
progress2.ProgressTintColor = UIColor.FromRGB (66,255,63);
progress2.TrackTintColor = UIColor.FromRGB (197,255,132);
```

## <a name="using-uiappearance-in-xamarinforms"></a>Uso di UIAppearance in xamarin. Forms

L'API di aspetto può essere utile quando [lo stile dell'app iOS](~/xamarin-forms/platform/ios/theme.md#uiappearance) nelle soluzioni xamarin. Forms. Alcune righe nel `AppDelegate` classe può rivelarsi utile per implementare uno schema di colore specifico senza dover creare un [renderer personalizzati](~/xamarin-forms/app-fundamentals/custom-renderer/index.md).

### <a name="custom-themes-and-uiappearance"></a>UIAppearance e temi personalizzati

iOS consente molti attributi visivi dell'utente ai controlli dell'interfaccia a essere "tema" usando il *UIAppearance* API forzare tutte le istanze di un particolare controllo hanno lo stesso aspetto. Questa funzionalità è esposta come proprietà di aspetto molte classi di controlli dell'interfaccia utente, non le singole istanze del controllo. L'impostazione di una proprietà di visualizzazione in statico `Appearance` proprietà ha effetto su tutti i controlli di quel tipo nell'applicazione.

Per comprendere meglio il concetto, si consideri un esempio.

Per modificare uno specifico `UISegmentedControl` per avere una tonalità di colore Magenta, si fa riferimento il controllo specifico nella nostra schermata simile alla seguente in `ViewDidLoad`:

```csharp
sg1.TintColor = UIColor.Magenta;
```

In alternativa, impostare il valore nel riquadro delle proprietà della finestra di progettazione: 

[![](introduction-to-the-appearance-api-images/propertiespadtint.png "Proprietà riempimento tinta")](introduction-to-the-appearance-api-images/propertiespadtint.png#lightbox)

L'immagine seguente illustra che configura la tonalità sulla solo il controllo denominato 'sg1'.

[![](introduction-to-the-appearance-api-images/image53.png "Impostazione della tonalità controllo individuale")](introduction-to-the-appearance-api-images/image53.png#lightbox)

Per impostare molti controlli in questo modo sarebbe completamente inefficace, pertanto è invece possibile impostare il metodo statico `Appearance` proprietà sulla classe stessa. Questa operazione è illustrata nel codice seguente:

```csharp
UISegmentedControl.Appearance.TintColor = UIColor.Magenta;
```

A questo punto, l'immagine seguente illustra entrambi i controlli segmentati con l'aspetto impostato su Magenta:

[![](introduction-to-the-appearance-api-images/image54.png "Impostazione della tonalità di controllo di aspetto")](introduction-to-the-appearance-api-images/image54.png#lightbox)

`Appearance` proprietà devono essere impostate all'inizio del ciclo di vita dell'applicazione, ad esempio in dell'AppDelegate `FinishedLaunching` evento, o in un ViewController prima i controlli interessati vengono visualizzati.

Vedere le [Introduzione all'API di aspetto](~/ios/user-interface/ios-ui/introduction-to-the-appearance-api.md) per informazioni più dettagliate.

## <a name="related-links"></a>Collegamenti correlati

- [Aspetto (esempio)](https://developer.xamarin.com/samples/monotouch/Appearance/)
- [Riferimento al protocollo UIAppearance](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UIAppearance_Protocol/)
- [Aspetto in xamarin. Forms](~/xamarin-forms/platform/ios/theme.md#uiappearance)
