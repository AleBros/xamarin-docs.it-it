---
title: Opzioni di layout in xamarin. Forms
description: Tutte le visualizzazioni di xamarin. Forms ha proprietà HorizontalOptions e VerticalOptions, di tipo LayoutOptions. Questo articolo illustra l'effetto ogni valore LayoutOptions sull'allineamento e espansione di una vista.
ms.prod: xamarin
ms.assetid: 7CAB5631-5153-4DEF-8AD7-C6011CE44307
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/10/2017
ms.openlocfilehash: afeae9d893b2ebe161943286d348f7a7a1074b0b
ms.sourcegitcommit: b23a107b0fe3d2f814ae35b52a5855b6ce2a3513
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/20/2019
ms.locfileid: "65925394"
---
# <a name="layout-options-in-xamarinforms"></a>Opzioni di layout in xamarin. Forms

[![Scaricare l'esempio](~/media/shared/download.png) scaricare l'esempio](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/LayoutOptions/)

_Tutte le visualizzazioni di xamarin. Forms ha proprietà HorizontalOptions e VerticalOptions, di tipo LayoutOptions. Questo articolo illustra l'effetto ogni valore LayoutOptions sull'allineamento e espansione di una vista._

## <a name="overview"></a>Panoramica

Il [ `LayoutOptions` ](xref:Xamarin.Forms.LayoutOptions) struttura sono due le preferenze di layout:

- **Allineamento** : la visualizzazione preferita dell'allineamento, che determina la posizione e dimensioni all'interno del layout del padre.
- **Espansione** : usato solo da un [ `StackLayout` ](xref:Xamarin.Forms.StackLayout)e indica se la visualizzazione deve utilizzare lo spazio aggiuntivo, se disponibile.

Queste preferenze di layout possono essere applicate a un [ `View` ](xref:Xamarin.Forms.View)relativo al controllo padre, impostando il [ `HorizontalOptions` ](xref:Xamarin.Forms.View.HorizontalOptions) o [ `VerticalOptions` ](xref:Xamarin.Forms.View.VerticalOptions) proprietà del `View` a uno dei campi pubblici di [ `LayoutOptions` ](xref:Xamarin.Forms.LayoutOptions) struttura. I campi pubblici sono i seguenti:

- [`Start`](xref:Xamarin.Forms.LayoutOptions.Start)
- [`Center`](xref:Xamarin.Forms.LayoutOptions.Center)
- [`End`](xref:Xamarin.Forms.LayoutOptions.End)
- [`Fill`](xref:Xamarin.Forms.LayoutOptions.Fill)
- [`StartAndExpand`](xref:Xamarin.Forms.LayoutOptions.StartAndExpand)
- [`CenterAndExpand`](xref:Xamarin.Forms.LayoutOptions.CenterAndExpand)
- [`EndAndExpand`](xref:Xamarin.Forms.LayoutOptions.EndAndExpand)
- [`FillAndExpand`](xref:Xamarin.Forms.LayoutOptions.FillAndExpand)

Il `Start`, `Center`, `End`, e `Fill` campi vengono usati per definire l'allineamento della visualizzazione all'interno del layout del padre:

- Per l'allineamento orizzontale, [ `Start` ](xref:Xamarin.Forms.LayoutOptions.Start) posizioni il [ `View` ](xref:Xamarin.Forms.View) sul lato sinistro del layout del padre e per l'allineamento verticale, posiziona il `View` all'inizio del layout del padre.
- Per l'allineamento orizzontale e verticale [ `Center` ](xref:Xamarin.Forms.LayoutOptions.Center) Centra orizzontalmente o verticalmente il [ `View` ](xref:Xamarin.Forms.View).
- Per l'allineamento orizzontale, [ `End` ](xref:Xamarin.Forms.LayoutOptions.End) posizioni il [ `View` ](xref:Xamarin.Forms.View) sul lato destro di layout del padre e per l'allineamento verticale, posiziona il `View` nella parte inferiore del layout del padre.
- Per l'allineamento orizzontale, [ `Fill` ](xref:Xamarin.Forms.LayoutOptions.Fill) assicura che il [ `View` ](xref:Xamarin.Forms.View) riempie la larghezza del layout del padre e per l'allineamento verticale, che assicura la `View` riempie il altezza del layout del padre.

Il `StartAndExpand`, `CenterAndExpand`, `EndAndExpand`, e `FillAndExpand` vengono utilizzati per definire la preferenza di allineamento, e indica se la vista occupa più spazio se disponibile entro l'elemento padre [ `StackLayout` ](xref:Xamarin.Forms.StackLayout).

> [!NOTE]
> Il valore predefinito di una vista [ `HorizontalOptions` ](xref:Xamarin.Forms.View.HorizontalOptions) e [ `VerticalOptions` ](xref:Xamarin.Forms.View.VerticalOptions) è di proprietà [ `LayoutOptions.Fill` ](xref:Xamarin.Forms.LayoutOptions.Fill).

<a name="alignment" />

## <a name="alignment"></a>Allineamento

Controlla l'allineamento come una vista viene posizionata all'interno del layout del padre quando il layout del padre contiene spazio inutilizzato (vale a dire, il layout del padre è maggiore della dimensione combinata di tutti gli elementi figlio).

Oggetto [ `StackLayout` ](xref:Xamarin.Forms.StackLayout) rispetta solo le `Start`, `Center`, `End`, e `Fill` [ `LayoutOptions` ](xref:Xamarin.Forms.LayoutOptions) campi nelle viste figlio che sono nella direzione opposta per il `StackLayout` orientamento. Pertanto di visualizzazioni figlio all'interno di un orientamento verticale `StackLayout` impostabile loro [ `HorizontalOptions` ](xref:Xamarin.Forms.View.HorizontalOptions) le proprietà su uno del `Start`, `Center`, `End`, o `Fill` campi. In modo analogo, di visualizzazioni figlio all'interno di un orientato orizzontalmente `StackLayout` impostabile loro [ `VerticalOptions` ](xref:Xamarin.Forms.View.VerticalOptions) le proprietà su uno del `Start`, `Center`, `End`, o `Fill` campi.

Oggetto [ `StackLayout` ](xref:Xamarin.Forms.StackLayout) non rispetta il `Start`, `Center`, `End`, e `Fill` [ `LayoutOptions` ](xref:Xamarin.Forms.LayoutOptions) campi nelle viste figlio che sono nella stessa direzione il `StackLayout` orientamento. Pertanto, un orientato verticalmente `StackLayout` ignora il `Start`, `Center`, `End`, o `Fill` campi se sono impostati sul [ `VerticalOptions` ](xref:Xamarin.Forms.View.VerticalOptions) le proprietà delle visualizzazioni figlio. Analogamente, un orientato orizzontalmente `StackLayout` ignora il `Start`, `Center`, `End`, o `Fill` campi se sono impostati sulla [ `HorizontalOptions` ](xref:Xamarin.Forms.View.HorizontalOptions) le proprietà delle visualizzazioni figlio.

> [!NOTE]
> [`LayoutOptions.Fill`](xref:Xamarin.Forms.LayoutOptions.Fill) in genere le sostituzioni dimensioni richieste specificate utilizzando il [ `HeightRequest` ](xref:Xamarin.Forms.VisualElement.HeightRequest) e [ `WidthRequest` ](xref:Xamarin.Forms.VisualElement.WidthRequest) proprietà.

L'esempio di codice XAML seguente illustra un orientato verticalmente [ `StackLayout` ](xref:Xamarin.Forms.StackLayout) in cui ogni elemento figlio [ `Label` ](xref:Xamarin.Forms.Label) imposta relativo [ `HorizontalOptions` ](xref:Xamarin.Forms.View.HorizontalOptions) proprietà per uno dei campi di quattro allineamento dal [ `LayoutOptions` ](xref:Xamarin.Forms.LayoutOptions) struttura:

```xaml
<StackLayout Margin="0,20,0,0">
  ...
  <Label Text="Start" BackgroundColor="Gray" HorizontalOptions="Start" />
  <Label Text="Center" BackgroundColor="Gray" HorizontalOptions="Center" />
  <Label Text="End" BackgroundColor="Gray" HorizontalOptions="End" />
  <Label Text="Fill" BackgroundColor="Gray" HorizontalOptions="Fill" />
</StackLayout>
```

Seguito è riportato il codice c# equivalente:

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

Il codice genera il layout illustrato negli screenshot seguenti:

[![](layout-options-images/alignment.png "Opzioni di Layout di allineamento")](layout-options-images/alignment-large.png#lightbox "opzioni di Layout di allineamento")

<a name="expansion" />

## <a name="expansion"></a>Espansione

Espansione controlla se una vista occupano più spazio, se disponibile, all'interno di un [ `StackLayout` ](xref:Xamarin.Forms.StackLayout). Se il `StackLayout` contiene spazio inutilizzato (vale a dire, il `StackLayout` è maggiore della dimensione combinata di tutti i relativi figli), lo spazio inutilizzato è ugualmente condiviso da tutte le visualizzazioni figlio che richiedono l'espansione tramite l'impostazione loro [ `HorizontalOptions` ](xref:Xamarin.Forms.View.HorizontalOptions)oppure [ `VerticalOptions` ](xref:Xamarin.Forms.View.VerticalOptions) le proprietà di un [ `LayoutOptions` ](xref:Xamarin.Forms.LayoutOptions) campo che usa il `AndExpand` suffisso. Si noti che quando tutto lo spazio di `StackLayout` viene utilizzato, le opzioni di espansione non hanno alcun effetto.

Oggetto [ `StackLayout` ](xref:Xamarin.Forms.StackLayout) solo possibile espandere le visualizzazioni figlio nella direzione dell'orientamento. Di conseguenza, un orientato verticalmente `StackLayout` ampliabili visualizzazioni figlio che impostare loro [ `VerticalOptions` ](xref:Xamarin.Forms.View.VerticalOptions) proprietà su uno del `StartAndExpand`, `CenterAndExpand`, `EndAndExpand`, o `FillAndExpand` i campi, se il `StackLayout` contiene spazio inutilizzato. Analogamente, un orientato orizzontalmente `StackLayout` ampliabili visualizzazioni figlio che impostare loro [ `HorizontalOptions` ](xref:Xamarin.Forms.View.HorizontalOptions) proprietà su uno del `StartAndExpand`, `CenterAndExpand`, `EndAndExpand`, o `FillAndExpand` i campi, se il `StackLayout` contiene spazio inutilizzato.

Oggetto [ `StackLayout` ](xref:Xamarin.Forms.StackLayout) non è possibile espandere le visualizzazioni figlio nella direzione opposta a quella dell'orientamento. Pertanto, in un controllo orientato verticalmente `StackLayout`, impostando il [ `HorizontalOptions` ](xref:Xamarin.Forms.View.HorizontalOptions) proprietà in una visualizzazione figlio [ `StartAndExpand` ](xref:Xamarin.Forms.LayoutOptions.StartAndExpand) ha lo stesso effetto impostando la proprietà su [ `Start`](xref:Xamarin.Forms.LayoutOptions.Start).

> [!NOTE]
> Si noti che l'abilitazione di espansione non cambia le dimensioni di una visualizzazione a meno che non usa [ `LayoutOptions.FillAndExpand` ](xref:Xamarin.Forms.LayoutOptions.FillAndExpand).

L'esempio di codice XAML seguente illustra un orientato verticalmente [ `StackLayout` ](xref:Xamarin.Forms.StackLayout) in cui ogni elemento figlio [ `Label` ](xref:Xamarin.Forms.Label) imposta relativo [ `VerticalOptions` ](xref:Xamarin.Forms.View.VerticalOptions) proprietà per uno dei campi di quattro espansione dal [ `LayoutOptions` ](xref:Xamarin.Forms.LayoutOptions) struttura:

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

Seguito è riportato il codice c# equivalente:

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

Il codice genera il layout illustrato negli screenshot seguenti:

[![](layout-options-images/expansion.png "Opzioni di Layout di espansione")](layout-options-images/expansion-large.png#lightbox "opzioni di Layout di espansione")

Ciascuna [ `Label` ](xref:Xamarin.Forms.Label) occupa la stessa quantità di spazio all'interno di [ `StackLayout` ](xref:Xamarin.Forms.StackLayout). Tuttavia, solo l'elemento finale `Label`, che imposta relativi [ `VerticalOptions` ](xref:Xamarin.Forms.View.VerticalOptions) proprietà [ `FillAndExpand` ](xref:Xamarin.Forms.LayoutOptions.FillAndExpand) ha una dimensione diversa. Inoltre, ogni `Label` è separata da un piccolo red [ `BoxView` ](xref:Xamarin.Forms.BoxView), che consente lo spazio di `Label` occupa per essere facilmente visualizzati.

## <a name="summary"></a>Riepilogo

Questo articolo ha illustrato l'effetto che ogni [ `LayoutOptions` ](xref:Xamarin.Forms.LayoutOptions) ha valore struttura in allineamento e l'espansione di una vista, rispetto al padre. Il `Start`, `Center`, `End`, e `Fill` campi vengono usati per definire l'allineamento della visualizzazione all'interno del layout, padre e il `StartAndExpand`, `CenterAndExpand`, `EndAndExpand`, e `FillAndExpand` campi vengono usati per definire la preferenza di allineamento e per determinare se la vista occupano più spazio, se disponibile, all'interno di un [ `StackLayout` ](xref:Xamarin.Forms.StackLayout).



## <a name="related-links"></a>Collegamenti correlati

- [LayoutOptions (esempio)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/LayoutOptions/)
- [LayoutOptions](xref:Xamarin.Forms.LayoutOptions)
