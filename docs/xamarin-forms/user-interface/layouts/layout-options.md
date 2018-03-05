---
title: LayoutOptions
description: Tutte le viste di xamarin. Forms contiene HorizontalOptions e VerticalOptions di tipo LayoutOptions. In questo articolo viene illustrato l'effetto ogni valore LayoutOptions sull'allineamento e espansione di una vista.
ms.topic: article
ms.prod: xamarin
ms.assetid: 7CAB5631-5153-4DEF-8AD7-C6011CE44307
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/10/2017
ms.openlocfilehash: 978985c4e9803fad33760e4b40ab73d57f3ec420
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2018
---
# <a name="layoutoptions"></a>LayoutOptions

_Tutte le viste di xamarin. Forms contiene HorizontalOptions e VerticalOptions di tipo LayoutOptions. In questo articolo viene illustrato l'effetto ogni valore LayoutOptions sull'allineamento e espansione di una vista._

## <a name="overview"></a>Panoramica

Il [ `LayoutOptions` ](https://developer.xamarin.com/api/type/Xamarin.Forms.LayoutOptions/) struttura sono due le preferenze di layout:

- **Allineamento** : la visualizzazione preferita dell'allineamento, che determina la posizione e dimensione all'interno del layout del padre.
- **Espansione** -usato solo da un [ `StackLayout` ](https://developer.xamarin.com/api/type/Xamarin.Forms.StackLayout/)e indica se la vista deve utilizzare lo spazio aggiuntivo, se disponibile.

Queste preferenze di layout possono essere applicate a un [ `View` ](https://developer.xamarin.com/api/type/Xamarin.Forms.View/), relativo all'elemento padre, impostando il [ `HorizontalOptions` ](https://developer.xamarin.com/api/property/Xamarin.Forms.View.HorizontalOptions/) o [ `VerticalOptions` ](https://developer.xamarin.com/api/property/Xamarin.Forms.View.VerticalOptions/) proprietà del `View` a uno dei campi pubblici di [ `LayoutOptions` ](https://developer.xamarin.com/api/type/Xamarin.Forms.LayoutOptions/) struttura. I campi pubblici sono i seguenti:

- [`Start`](https://developer.xamarin.com/api/field/Xamarin.Forms.LayoutOptions.Start/)
- [`Center`](https://developer.xamarin.com/api/field/Xamarin.Forms.LayoutOptions.Center/)
- [`End`](https://developer.xamarin.com/api/field/Xamarin.Forms.LayoutOptions.End/)
- [`Fill`](https://developer.xamarin.com/api/field/Xamarin.Forms.LayoutOptions.Fill/)
- [`StartAndExpand`](https://developer.xamarin.com/api/field/Xamarin.Forms.LayoutOptions.StartAndExpand/)
- [`CenterAndExpand`](https://developer.xamarin.com/api/field/Xamarin.Forms.LayoutOptions.CenterAndExpand/)
- [`EndAndExpand`](https://developer.xamarin.com/api/field/Xamarin.Forms.LayoutOptions.EndAndExpand/)
- [`FillAndExpand`](https://developer.xamarin.com/api/field/Xamarin.Forms.LayoutOptions.FillAndExpand/)

Il `Start`, `Center`, `End`, e `Fill` campi vengono utilizzati per definire l'allineamento della visualizzazione all'interno del layout padre:

- Per l'allineamento orizzontale, [ `Start` ](https://developer.xamarin.com/api/field/Xamarin.Forms.LayoutOptions.Start/) posizioni di [ `View` ](https://developer.xamarin.com/api/type/Xamarin.Forms.View/) sul lato sinistro del layout del padre e per l'allineamento verticale, posiziona il `View` nella parte superiore del layout dell'elemento padre.
- Per l'allineamento orizzontale e verticale, [ `Center` ](https://developer.xamarin.com/api/field/Xamarin.Forms.LayoutOptions.Center/) Centra orizzontalmente o verticalmente il [ `View` ](https://developer.xamarin.com/api/type/Xamarin.Forms.View/).
- Per l'allineamento orizzontale, [ `End` ](https://developer.xamarin.com/api/field/Xamarin.Forms.LayoutOptions.End/) posizioni di [ `View` ](https://developer.xamarin.com/api/type/Xamarin.Forms.View/) sul lato destro e per l'allineamento verticale del layout del padre, viene posizionato il `View` nella parte inferiore del layout del padre.
- Per l'allineamento orizzontale, [ `Fill` ](https://developer.xamarin.com/api/field/Xamarin.Forms.LayoutOptions.Fill/) assicura che il [ `View` ](https://developer.xamarin.com/api/type/Xamarin.Forms.View/) riempie la larghezza del layout del padre e per l'allineamento verticale, assicura che il `View` riempie il altezza del layout del padre.

Il `StartAndExpand`, `CenterAndExpand`, `EndAndExpand`, e `FillAndExpand` valori vengono utilizzati per definire la preferenza di allineamento, e se la vista occupa più spazio se è disponibile nel controllo padre [ `StackLayout` ](https://developer.xamarin.com/api/type/Xamarin.Forms.StackLayout/).

> [!NOTE]
> Il valore predefinito di una vista [ `HorizontalOptions` ](https://developer.xamarin.com/api/property/Xamarin.Forms.View.HorizontalOptions/) e [ `VerticalOptions` ](https://developer.xamarin.com/api/property/Xamarin.Forms.View.VerticalOptions/) è di proprietà [ `LayoutOptions.Fill` ](https://developer.xamarin.com/api/field/Xamarin.Forms.LayoutOptions.Fill/).

<a name="alignment" />

## <a name="alignment"></a>Allineamento

Allineamento di controlli come una vista è posizionata all'interno del layout padre quando il layout padre contiene spazio inutilizzato (ovvero, il layout del padre è maggiore della dimensione combinata di tutti gli elementi figlio).

Oggetto [ `StackLayout` ](https://developer.xamarin.com/api/type/Xamarin.Forms.StackLayout/) solo rispetta il `Start`, `Center`, `End`, e `Fill` [ `LayoutOptions` ](https://developer.xamarin.com/api/type/Xamarin.Forms.LayoutOptions/) campi visualizzazioni figlio che sono nella direzione opposta per il `StackLayout` orientamento. Pertanto, di visualizzazioni figlio all'interno di un controllo orientato verticalmente `StackLayout` possibile impostare loro [ `HorizontalOptions` ](https://developer.xamarin.com/api/property/Xamarin.Forms.View.HorizontalOptions/) proprietà su uno del `Start`, `Center`, `End`, o `Fill` campi. Analogamente, figlio viste all'interno di un orientamento orizzontale `StackLayout` possibile impostare i relativi [ `VerticalOptions` ](https://developer.xamarin.com/api/property/Xamarin.Forms.View.VerticalOptions/) proprietà a uno del `Start`, `Center`, `End`, o `Fill` campi.

Oggetto [ `StackLayout` ](https://developer.xamarin.com/api/type/Xamarin.Forms.StackLayout/) non rispetta il `Start`, `Center`, `End`, e `Fill` [ `LayoutOptions` ](https://developer.xamarin.com/api/type/Xamarin.Forms.LayoutOptions/) visualizzazioni figlio che sono nella stessa direzione di campi il `StackLayout` orientamento. Pertanto, un orientato verticalmente `StackLayout` ignora il `Start`, `Center`, `End`, o `Fill` campi se sono impostati sul [ `VerticalOptions` ](https://developer.xamarin.com/api/property/Xamarin.Forms.View.VerticalOptions/) le proprietà delle visualizzazioni figlio. Analogamente, un orientamento orizzontale `StackLayout` ignora il `Start`, `Center`, `End`, o `Fill` campi se sono impostati sul [ `HorizontalOptions` ](https://developer.xamarin.com/api/property/Xamarin.Forms.View.HorizontalOptions/) le proprietà delle visualizzazioni figlio.

> [!NOTE]
> [`LayoutOptions.Fill`](https://developer.xamarin.com/api/field/Xamarin.Forms.LayoutOptions.Fill/) in genere sostituzioni dimensioni richieste specificate utilizzando il [ `HeightRequest` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.HeightRequest/) e [ `WidthRequest` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.WidthRequest/) proprietà.

L'esempio di codice XAML seguente viene illustrato un orientato verticalmente [ `StackLayout` ](https://developer.xamarin.com/api/type/Xamarin.Forms.StackLayout/) in cui ogni elemento figlio [ `Label` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/) imposta relativo [ `HorizontalOptions` ](https://developer.xamarin.com/api/property/Xamarin.Forms.View.HorizontalOptions/) proprietà per uno dei campi da quattro allineamento di [ `LayoutOptions` ](https://developer.xamarin.com/api/type/Xamarin.Forms.LayoutOptions/) struttura:

```xaml
<StackLayout Margin="0,20,0,0">
  ...
  <Label Text="Start" BackgroundColor="Gray" HorizontalOptions="Start" />
  <Label Text="Center" BackgroundColor="Gray" HorizontalOptions="Center" />
  <Label Text="End" BackgroundColor="Gray" HorizontalOptions="End" />
  <Label Text="Fill" BackgroundColor="Gray" HorizontalOptions="Fill" />
</StackLayout>
```

Il codice c# equivalente è illustrato di seguito:

```csharp
Content = new StackLayout
{
  Margin = new Thickness(0, 20, 0, 0),
  Children = {
    ...
    new Label { Text = "Start", BackgroundColor = Color.Gray, HorizontalOptions = LayoutOptions.Start },
    new Label { Text = "Center", BackgroundColor = Color.Gray, HorizontalOptions = LayoutOptions.Center },
    new Label { Text = "End", BackgroundColor = Color.Gray, HorizontalOptions = LayoutOptions.End },
    new Label { Text = "Fill", BackgroundColor = Color.Gray, HorizontalOptions = LayoutOptions.Fill }
  }
};
```

Il codice comporta il layout mostrato nelle schermate seguenti:

[![](layout-options-images/alignment.png "Opzioni di Layout di allineamento")](layout-options-images/alignment-large.png "opzioni di Layout di allineamento")

<a name="expansion" />

## <a name="expansion"></a>Espansione

Espansione controlla se una vista occupano più spazio, se disponibile, all'interno di un [ `StackLayout` ](https://developer.xamarin.com/api/type/Xamarin.Forms.StackLayout/). Se il `StackLayout` contiene spazio inutilizzato (, ovvero il `StackLayout` è maggiore della dimensione combinata di tutti i relativi elementi figlio), lo spazio inutilizzato è condivisa equamente da tutte le visualizzazioni figlio che richiedono l'espansione impostando i relativi [ `HorizontalOptions` ](https://developer.xamarin.com/api/property/Xamarin.Forms.View.HorizontalOptions/)o [ `VerticalOptions` ](https://developer.xamarin.com/api/property/Xamarin.Forms.View.VerticalOptions/) proprietà per un [ `LayoutOptions` ](https://developer.xamarin.com/api/type/Xamarin.Forms.LayoutOptions/) campo che utilizza il `AndExpand` suffisso. Si noti che quando tutto lo spazio di `StackLayout` viene utilizzato, le opzioni di espansione non hanno alcun effetto.

Oggetto [ `StackLayout` ](https://developer.xamarin.com/api/type/Xamarin.Forms.StackLayout/) possibile espandere solo visualizzazioni figlio nella direzione di orientamento. Pertanto un orientato verticalmente `StackLayout` possibile espandere le visualizzazioni figlio che impostare loro [ `VerticalOptions` ](https://developer.xamarin.com/api/property/Xamarin.Forms.View.VerticalOptions/) proprietà su uno del `StartAndExpand`, `CenterAndExpand`, `EndAndExpand`, o `FillAndExpand` i campi, se il `StackLayout` contiene spazio inutilizzato. Analogamente, un orientamento orizzontale `StackLayout` espandibili visualizzazioni figlio che impostare loro [ `HorizontalOptions` ](https://developer.xamarin.com/api/property/Xamarin.Forms.View.HorizontalOptions/) proprietà a uno del `StartAndExpand`, `CenterAndExpand`, `EndAndExpand`, o `FillAndExpand` i campi, se il `StackLayout` contiene spazio inutilizzato.

Oggetto [ `StackLayout` ](https://developer.xamarin.com/api/type/Xamarin.Forms.StackLayout/) non è possibile espandere visualizzazioni figlio nella direzione opposta relativo orientamento. Pertanto, in orientamento verticale `StackLayout`, l'impostazione di [ `HorizontalOptions` ](https://developer.xamarin.com/api/property/Xamarin.Forms.View.HorizontalOptions/) proprietà in una visualizzazione figlio [ `StartAndExpand` ](https://developer.xamarin.com/api/field/Xamarin.Forms.LayoutOptions.StartAndExpand/) ha lo stesso effetto dell'impostazione della proprietà [ `Start`](https://developer.xamarin.com/api/field/Xamarin.Forms.LayoutOptions.Start/).

> [!NOTE]
> Si noti che l'abilitazione di espansione non modificare le dimensioni di una visualizzazione a meno che non usa [ `LayoutOptions.FillAndExpand` ](https://developer.xamarin.com/api/field/Xamarin.Forms.LayoutOptions.FillAndExpand/).

L'esempio di codice XAML seguente viene illustrato un orientato verticalmente [ `StackLayout` ](https://developer.xamarin.com/api/type/Xamarin.Forms.StackLayout/) in cui ogni elemento figlio [ `Label` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/) imposta relativo [ `VerticalOptions` ](https://developer.xamarin.com/api/property/Xamarin.Forms.View.VerticalOptions/) proprietà per uno dei campi da quattro espansione di [ `LayoutOptions` ](https://developer.xamarin.com/api/type/Xamarin.Forms.LayoutOptions/) struttura:

```xaml
<StackLayout Margin="0,20,0,0">
  ...
  <BoxView BackgroundColor="Red" HeightRequest="1" />
  <Label Text="Start" BackgroundColor="Gray" VerticalOptions="StartAndExpand" />
  <BoxView BackgroundColor="Red" HeightRequest="1" />
  <Label Text="Center" BackgroundColor="Gray" VerticalOptions="CenterAndExpand" />
  <BoxView BackgroundColor="Red" HeightRequest="1" />
  <Label Text="End" BackgroundColor="Gray" VerticalOptions="EndAndExpand" />
  <BoxView BackgroundColor="Red" HeightRequest="1" />
  <Label Text="Fill" BackgroundColor="Gray" VerticalOptions="FillAndExpand" />
  <BoxView BackgroundColor="Red" HeightRequest="1" />
</StackLayout>
```

Il codice c# equivalente è illustrato di seguito:

```csharp
Content = new StackLayout
{
  Margin = new Thickness(0, 20, 0, 0),
  Children = {
    ...
    new BoxView { BackgroundColor = Color.Red, HeightRequest = 1 },
    new Label { Text = "StartAndExpand", BackgroundColor = Color.Gray, VerticalOptions = LayoutOptions.StartAndExpand },
    new BoxView { BackgroundColor = Color.Red, HeightRequest = 1 },
    new Label { Text = "CenterAndExpand", BackgroundColor = Color.Gray, VerticalOptions = LayoutOptions.CenterAndExpand },
    new BoxView { BackgroundColor = Color.Red, HeightRequest = 1 },
    new Label { Text = "EndAndExpand", BackgroundColor = Color.Gray, VerticalOptions = LayoutOptions.EndAndExpand },
    new BoxView { BackgroundColor = Color.Red, HeightRequest = 1 },
    new Label { Text = "FillAndExpand", BackgroundColor = Color.Gray, VerticalOptions = LayoutOptions.FillAndExpand },
    new BoxView { BackgroundColor = Color.Red, HeightRequest = 1 }
  }
};
```

Il codice comporta il layout mostrato nelle schermate seguenti:

[![](layout-options-images/expansion.png "Opzioni di Layout di espansione")](layout-options-images/expansion-large.png "opzioni di Layout di espansione")

Ogni [ `Label` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/) occupa la stessa quantità di spazio all'interno di [ `StackLayout` ](https://developer.xamarin.com/api/type/Xamarin.Forms.StackLayout/). Tuttavia, solo gli ultimi `Label`, che imposta il relativo [ `VerticalOptions` ](https://developer.xamarin.com/api/property/Xamarin.Forms.View.VerticalOptions/) proprietà [ `FillAndExpand` ](https://developer.xamarin.com/api/field/Xamarin.Forms.LayoutOptions.FillAndExpand/) ha una dimensione diversa. Inoltre, ogni `Label` è separato da una piccola rossa [ `BoxView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.BoxView/), che consente lo spazio di `Label` occupa per essere facilmente visualizzati.

## <a name="summary"></a>Riepilogo

Questo articolo ha descritto l'effetto che ogni [ `LayoutOptions` ](https://developer.xamarin.com/api/type/Xamarin.Forms.LayoutOptions/) valore struttura ha sul allineamento e espansione di una vista, rispetto al padre. Il `Start`, `Center`, `End`, e `Fill` campi vengono utilizzati per definire l'allineamento della visualizzazione all'interno del layout, padre e `StartAndExpand`, `CenterAndExpand`, `EndAndExpand`, e `FillAndExpand` utilizzati per definire i campi la preferenza di allineamento e per determinare se la vista occupa più spazio, se disponibile, all'interno di un [ `StackLayout` ](https://developer.xamarin.com/api/type/Xamarin.Forms.StackLayout/).



## <a name="related-links"></a>Collegamenti correlati

- [LayoutOptions (esempio)](https://developer.xamarin.com/samples/xamarin-forms/userinterface/layoutoptions/)
- [LayoutOptions](https://developer.xamarin.com/api/type/Xamarin.Forms.LayoutOptions/)
