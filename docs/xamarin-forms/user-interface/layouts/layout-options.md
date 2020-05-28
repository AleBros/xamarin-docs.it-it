---
title: Opzioni di layout inXamarin.Forms
description: Ogni Xamarin.Forms vista dispone di proprietà HorizontalOptions e VerticalOptions di tipo LayoutOptions. Questo articolo illustra l'effetto di ogni valore LayoutOptions sull'allineamento e sull'espansione di una vista.
ms.prod: ''
ms.assetid: ''
ms.technology: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 17f4e76f9bef71352cabddfba9397e95bcdd24d3
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/28/2020
ms.locfileid: "84138031"
---
# <a name="layout-options-in-xamarinforms"></a>Opzioni di layout inXamarin.Forms

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-layoutoptions)

_Ogni Xamarin.Forms vista dispone di proprietà HorizontalOptions e VerticalOptions di tipo LayoutOptions. Questo articolo illustra l'effetto di ogni valore LayoutOptions sull'allineamento e sull'espansione di una vista._

## <a name="overview"></a>Panoramica

La [`LayoutOptions`](xref:Xamarin.Forms.LayoutOptions) struttura incapsula due preferenze di layout:

- **Alignment** : allineamento preferenziale della visualizzazione, che ne determina la posizione e le dimensioni all'interno del layout padre.
- **Espansione** : usata solo da un [`StackLayout`](xref:Xamarin.Forms.StackLayout) e indica se la visualizzazione deve usare spazio aggiuntivo, se disponibile.

Queste preferenze di layout possono essere applicate a un oggetto [`View`](xref:Xamarin.Forms.View) , relativo all'elemento padre, impostando la [`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) [`VerticalOptions`](xref:Xamarin.Forms.View.VerticalOptions) proprietà o di `View` su uno dei campi pubblici della [`LayoutOptions`](xref:Xamarin.Forms.LayoutOptions) struttura. I campi pubblici sono i seguenti:

- [`Start`](xref:Xamarin.Forms.LayoutOptions.Start)
- [`Center`](xref:Xamarin.Forms.LayoutOptions.Center)
- [`End`](xref:Xamarin.Forms.LayoutOptions.End)
- [`Fill`](xref:Xamarin.Forms.LayoutOptions.Fill)
- [`StartAndExpand`](xref:Xamarin.Forms.LayoutOptions.StartAndExpand)
- [`CenterAndExpand`](xref:Xamarin.Forms.LayoutOptions.CenterAndExpand)
- [`EndAndExpand`](xref:Xamarin.Forms.LayoutOptions.EndAndExpand)
- [`FillAndExpand`](xref:Xamarin.Forms.LayoutOptions.FillAndExpand)

I `Start` `Center` campi,, `End` e `Fill` vengono utilizzati per definire l'allineamento della visualizzazione all'interno del layout padre:

- Per l'allineamento orizzontale, [`Start`](xref:Xamarin.Forms.LayoutOptions.Start) posiziona l'oggetto [`View`](xref:Xamarin.Forms.View) sul lato sinistro del layout padre e, per l'allineamento verticale, posiziona l'oggetto nella `View` parte superiore del layout padre.
- Per l'allineamento orizzontale e verticale, [`Center`](xref:Xamarin.Forms.LayoutOptions.Center) Centra orizzontalmente o verticalmente [`View`](xref:Xamarin.Forms.View) .
- Per l'allineamento orizzontale, [`End`](xref:Xamarin.Forms.LayoutOptions.End) posiziona sul [`View`](xref:Xamarin.Forms.View) lato destro del layout padre e, per l'allineamento verticale, posiziona l'oggetto nella parte `View` inferiore del layout padre.
- Per l'allineamento orizzontale, [`Fill`](xref:Xamarin.Forms.LayoutOptions.Fill) assicura che [`View`](xref:Xamarin.Forms.View) riempia la larghezza del layout padre e, per l'allineamento verticale, assicuri che `View` riempia l'altezza del layout padre.

I `StartAndExpand` valori,, `CenterAndExpand` `EndAndExpand` e `FillAndExpand` vengono usati per definire la preferenza di allineamento e se la vista occupa più spazio se disponibile all'interno dell'elemento padre [`StackLayout`](xref:Xamarin.Forms.StackLayout) .

> [!NOTE]
> Il valore predefinito delle proprietà [`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) e [`VerticalOptions`](xref:Xamarin.Forms.View.VerticalOptions) di una vista è [`LayoutOptions.Fill`](xref:Xamarin.Forms.LayoutOptions.Fill).

<a name="alignment" />

## <a name="alignment"></a>Allineamento

L'allineamento controlla il modo in cui una visualizzazione viene posizionata all'interno del layout padre quando il layout padre contiene spazio inutilizzato, ovvero il layout padre è maggiore della dimensione combinata di tutti i relativi elementi figlio.

Un oggetto [`StackLayout`](xref:Xamarin.Forms.StackLayout) rispetta solo `Start` i `Center` campi,, `End` e `Fill` [`LayoutOptions`](xref:Xamarin.Forms.LayoutOptions) nelle visualizzazioni figlio che si trovano nella direzione opposta all' `StackLayout` orientamento. Pertanto, le visualizzazioni figlio all'interno di un orientamento verticale `StackLayout` possono impostare le relative [`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) proprietà su uno dei `Start` `Center` campi,, `End` o `Fill` . Analogamente, le visualizzazioni figlio all'interno di un orientamento orizzontale `StackLayout` possono impostare le relative [`VerticalOptions`](xref:Xamarin.Forms.View.VerticalOptions) proprietà su uno dei `Start` `Center` campi,, `End` o `Fill` .

Un oggetto [`StackLayout`](xref:Xamarin.Forms.StackLayout) non rispetta i `Start` `Center` campi,, `End` e `Fill` [`LayoutOptions`](xref:Xamarin.Forms.LayoutOptions) nelle visualizzazioni figlio che sono nella stessa direzione dell' `StackLayout` orientamento. Pertanto, un orientamento verticale `StackLayout` Ignora i `Start` `Center` campi,, `End` o se sono `Fill` impostati sulle [`VerticalOptions`](xref:Xamarin.Forms.View.VerticalOptions) proprietà delle visualizzazioni figlio. Analogamente, un orientamento orizzontale `StackLayout` Ignora i `Start` campi, `Center` , `End` o `Fill` se sono impostati sulle [`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) proprietà delle visualizzazioni figlio.

> [!NOTE]
> [`LayoutOptions.Fill`](xref:Xamarin.Forms.LayoutOptions.Fill)in genere esegue l'override delle richieste di dimensioni specificate usando le [`HeightRequest`](xref:Xamarin.Forms.VisualElement.HeightRequest) [`WidthRequest`](xref:Xamarin.Forms.VisualElement.WidthRequest) proprietà e.

Nell'esempio di codice XAML riportato di seguito viene illustrata un'area orientata verticalmente [`StackLayout`](xref:Xamarin.Forms.StackLayout) in cui ogni elemento figlio [`Label`](xref:Xamarin.Forms.Label) imposta la [`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) proprietà su uno dei quattro campi di allineamento della [`LayoutOptions`](xref:Xamarin.Forms.LayoutOptions) struttura:

```xaml
<StackLayout Margin="0,20,0,0">
  ...
  <Label Text="Start" BackgroundColor="Gray" HorizontalOptions="Start" />
  <Label Text="Center" BackgroundColor="Gray" HorizontalOptions="Center" />
  <Label Text="End" BackgroundColor="Gray" HorizontalOptions="End" />
  <Label Text="Fill" BackgroundColor="Gray" HorizontalOptions="Fill" />
</StackLayout>
```

Il codice C# equivalente è illustrato di seguito:

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

Il codice restituisce il layout illustrato nelle schermate seguenti:

[![](layout-options-images/alignment.png "Alignment Layout Options")](layout-options-images/alignment-large.png#lightbox "Alignment Layout Options")

<a name="expansion" />

## <a name="expansion"></a>Espansione

L'espansione controlla se una vista occupa più spazio, se disponibile, all'interno di un [`StackLayout`](xref:Xamarin.Forms.StackLayout) . Se `StackLayout` contiene spazio inutilizzato, ovvero `StackLayout` è maggiore della dimensione combinata di tutti i relativi elementi figlio, lo spazio inutilizzato viene condiviso equamente da tutte le visualizzazioni figlio che richiedono l'espansione impostando le relative [`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) [`VerticalOptions`](xref:Xamarin.Forms.View.VerticalOptions) proprietà o su un [`LayoutOptions`](xref:Xamarin.Forms.LayoutOptions) campo che utilizza il `AndExpand` suffisso. Si noti che quando viene utilizzato tutto lo spazio in `StackLayout` , le opzioni di espansione non hanno alcun effetto.

Un elemento [`StackLayout`](xref:Xamarin.Forms.StackLayout) può espandere le viste figlio solo nella direzione del proprio orientamento. Un orientamento verticale `StackLayout` può pertanto espandere visualizzazioni figlio che ne impostano le [`VerticalOptions`](xref:Xamarin.Forms.View.VerticalOptions) proprietà su uno dei `StartAndExpand` `CenterAndExpand` campi,, `EndAndExpand` o `FillAndExpand` , se `StackLayout` contiene spazio inutilizzato. Analogamente, un orientamento orizzontale `StackLayout` può espandere le visualizzazioni figlio che ne impostano le [`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) proprietà su uno dei `StartAndExpand` `CenterAndExpand` campi,, `EndAndExpand` o `FillAndExpand` , se `StackLayout` contiene spazio inutilizzato.

Un oggetto [`StackLayout`](xref:Xamarin.Forms.StackLayout) non può espandere le visualizzazioni figlio nella direzione opposta all'orientamento. Pertanto, in un oggetto orientato verticalmente `StackLayout` , l'impostazione della [`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) proprietà su una visualizzazione figlio [`StartAndExpand`](xref:Xamarin.Forms.LayoutOptions.StartAndExpand) ha lo stesso effetto dell'impostazione della proprietà su [`Start`](xref:Xamarin.Forms.LayoutOptions.Start) .

> [!NOTE]
> Si noti che l'abilitazione dell'espansione non modifica le dimensioni di una visualizzazione a meno che non usi [`LayoutOptions.FillAndExpand`](xref:Xamarin.Forms.LayoutOptions.FillAndExpand) .

Nell'esempio di codice XAML riportato di seguito viene illustrata un'area orientata verticalmente [`StackLayout`](xref:Xamarin.Forms.StackLayout) in cui ogni elemento figlio [`Label`](xref:Xamarin.Forms.Label) imposta la [`VerticalOptions`](xref:Xamarin.Forms.View.VerticalOptions) proprietà su uno dei quattro campi di espansione della [`LayoutOptions`](xref:Xamarin.Forms.LayoutOptions) struttura:

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

Il codice C# equivalente è illustrato di seguito:

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

Il codice restituisce il layout illustrato nelle schermate seguenti:

[![](layout-options-images/expansion.png "Expansion Layout Options")](layout-options-images/expansion-large.png#lightbox "Expansion Layout Options")

Ognuno [`Label`](xref:Xamarin.Forms.Label) occupa la stessa quantità di spazio all'interno di [`StackLayout`](xref:Xamarin.Forms.StackLayout) . Tuttavia solo l'elemento `Label` finale che imposta la sua proprietà [`VerticalOptions`](xref:Xamarin.Forms.View.VerticalOptions) su [`FillAndExpand`](xref:Xamarin.Forms.LayoutOptions.FillAndExpand) ha una dimensione diversa. Inoltre, ognuno `Label` è separato da un piccolo rosso [`BoxView`](xref:Xamarin.Forms.BoxView) , che consente `Label` di visualizzare facilmente lo spazio occupato da.

## <a name="summary"></a>Riepilogo

In questo articolo è stato illustrato l'effetto di ogni [`LayoutOptions`](xref:Xamarin.Forms.LayoutOptions) valore di struttura sull'allineamento e sull'espansione di una vista rispetto al relativo elemento padre. I `Start` `Center` campi,, `End` e `Fill` vengono utilizzati per definire l'allineamento della visualizzazione all'interno del layout padre e i `StartAndExpand` campi, `CenterAndExpand` , `EndAndExpand` e `FillAndExpand` vengono utilizzati per definire la preferenza di allineamento e per determinare se la vista occupa più spazio, se disponibile, all'interno di un oggetto [`StackLayout`](xref:Xamarin.Forms.StackLayout) .

## <a name="related-links"></a>Collegamenti correlati

- [LayoutOptions (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-layoutoptions)
- [LayoutOptions](xref:Xamarin.Forms.LayoutOptions)
