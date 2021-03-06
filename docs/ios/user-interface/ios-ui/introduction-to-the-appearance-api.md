---
title: API di aspetto in Xamarin.iOS
description: iOS consente di applicare le impostazioni delle proprietà visive a un livello di classe statico anziché a singoli oggetti in modo che la modifica venga applicata a tutte le istanze di tale controllo nell'applicazione.
ms.prod: xamarin
ms.assetid: C1727F0C-82B1-D085-D46F-C6383FF04B16
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 11/15/2018
ms.openlocfilehash: 7a7f0fe9d0dc07d892686e6596f3cc09a2587513
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73003384"
---
# <a name="appearance-api-in-xamarinios"></a>API di aspetto in Xamarin.iOS

_iOS consente di applicare le impostazioni delle proprietà visive a un livello di classe statico anziché a singoli oggetti in modo che la modifica venga applicata a tutte le istanze di tale controllo nell'applicazione._

Questa funzionalità è esposta in Xamarin.iOS tramite una proprietà di `Appearance` statica in tutti i controlli UIKit che lo supportano. L'aspetto visivo (proprietà come il colore tinta e l'immagine di sfondo) può quindi essere facilmente personalizzato per dare all'applicazione un aspetto coerente. L'API di aspetto è stata introdotta in iOS 5 e, mentre alcune parti di esso sono state deprecate in iOS 9, è ancora un metodo efficace per realizzare alcuni effetti di stili e temi nelle app Xamarin.iOS.

## <a name="overview"></a>Panoramica

iOS consente di personalizzare l'aspetto di molti controlli UIKit per rendere i controlli standard conformi alla personalizzazione che si vuole applicare all'applicazione.

Esistono due modi diversi per applicare un aspetto personalizzato:

- **Direttamente in un'istanza di controllo** : è possibile impostare il colore della tinta, l'immagine di sfondo e la posizione del titolo (oltre ad altri attributi) su molti controlli, tra cui barre degli strumenti, barre di spostamento, pulsanti e cursori.

- **Impostare i valori predefiniti sulla proprietà statica Appearance** : gli attributi personalizzabili per ogni controllo vengono esposti tramite la proprietà statica `Appearance`. Tutte le personalizzazioni apportate a queste proprietà verranno utilizzate come predefinite per qualsiasi controllo del tipo creato dopo l'impostazione della proprietà.

L'applicazione di esempio Appearance illustra tutti e tre i metodi, come illustrato nelle schermate seguenti:

[![](introduction-to-the-appearance-api-images/appearance01-sml.png "The Appearance sample application demonstrates all three methods")](introduction-to-the-appearance-api-images/appearance01.png#lightbox)

A partire da iOS 8, il proxy di aspetto è stato esteso a TraitCollections.
 `AppearanceForTraitCollection` può essere usato per impostare l'aspetto predefinito in una raccolta di tratti particolare. Per ulteriori informazioni, vedere la Guida [introduttiva agli storyboard](~/ios/user-interface/storyboards/unified-storyboards.md) .

## <a name="setting-appearance-properties"></a>Impostazione delle proprietà relative all'aspetto

Nella prima schermata viene usata la classe di aspetto statico per applicare uno stile ai pulsanti e agli elementi gialli/arancioni come segue:

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

Gli stili degli elementi verdi sono impostati come segue, nel metodo `ViewDidLoad` che esegue l'override dei valori predefiniti e della classe statica *Appearance* :

```csharp
slider2.ThumbTintColor = UIColor.FromRGB (0,127,70); // dark green
slider2.MinimumTrackTintColor = UIColor.FromRGB (66,255,63);
slider2.MaximumTrackTintColor = UIColor.FromRGB (197,255,132);
```

```csharp
progress2.ProgressTintColor = UIColor.FromRGB (66,255,63);
progress2.TrackTintColor = UIColor.FromRGB (197,255,132);
```

## <a name="using-uiappearance-in-xamarinforms"></a>Uso di UIAppearance in Xamarin.Forms

L'API di aspetto può essere utile quando si applica [lo stile all'app iOS](~/xamarin-forms/platform/ios/formatting.md#uiappearance) nelle soluzioni Xamarin.Forms. Alcune righe della classe `AppDelegate` consentono di implementare una combinazione di colori specifica senza dover creare un [renderer personalizzato](~/xamarin-forms/app-fundamentals/custom-renderer/index.md).

### <a name="custom-themes-and-uiappearance"></a>Temi personalizzati e UIAppearance

iOS consente a molti attributi visivi dei controlli dell'interfaccia utente di essere "con tema" usando le API *UIAppearance* per forzare l'aspetto di tutte le istanze di un determinato controllo. Questo viene esposto come proprietà Appearance in molte classi di controlli dell'interfaccia utente, non nelle singole istanze del controllo. L'impostazione di una proprietà di visualizzazione sulla proprietà `Appearance` statica influiscono su tutti i controlli di quel tipo nell'applicazione.

Per comprendere meglio il concetto, prendere in considerazione un esempio.

Per modificare una `UISegmentedControl` specifica in modo che abbia una tinta magenta, si fa riferimento al controllo specifico sulla schermata simile alla seguente `ViewDidLoad`:

```csharp
sg1.TintColor = UIColor.Magenta;
```

In alternativa, impostare il valore nel riquadro proprietà della finestra di progettazione:

[![](introduction-to-the-appearance-api-images/propertiespadtint.png "Properties Pad Tint")](introduction-to-the-appearance-api-images/propertiespadtint.png#lightbox)

Nell'immagine seguente viene illustrato che questo imposta la tinta solo sul controllo denominato "SG1".

[![](introduction-to-the-appearance-api-images/image53.png "Setting the individual control tint")](introduction-to-the-appearance-api-images/image53.png#lightbox)

Per impostare molti controlli in questo modo sarebbe completamente inefficiente, quindi è possibile impostare la proprietà `Appearance` statica sulla classe stessa. Questa operazione è illustrata nel codice seguente:

```csharp
UISegmentedControl.Appearance.TintColor = UIColor.Magenta;
```

Nell'immagine seguente sono ora illustrati entrambi i controlli segmentati con l'aspetto impostato su Magenta:

[![](introduction-to-the-appearance-api-images/image54.png "Setting the Appearance control tint")](introduction-to-the-appearance-api-images/image54.png#lightbox)

`Appearance` le proprietà devono essere impostate all'inizio del ciclo di vita dell'applicazione, ad esempio nell'evento `FinishedLaunching` di AppDelegate o in un ViewController prima che vengano visualizzati i controlli interessati.

Per informazioni più dettagliate, vedere [Introduzione all'API di aspetto](~/ios/user-interface/ios-ui/introduction-to-the-appearance-api.md) .

## <a name="related-links"></a>Collegamenti correlati

- [Aspetto (esempio)](https://docs.microsoft.com/samples/xamarin/ios-samples/appearance)
- [Informazioni di riferimento sul protocollo UIAppearance](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UIAppearance_Protocol/)
- [Aspetto in Xamarin.Forms](~/xamarin-forms/platform/ios/formatting.md#uiappearance)
