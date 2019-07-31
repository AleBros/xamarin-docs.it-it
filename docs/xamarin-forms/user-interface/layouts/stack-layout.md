---
title: StackLayout xamarin. Forms
description: Questo articolo illustra come usare la classe StackLayout xamarin. Forms per presentare le raccolte di visualizzazioni in una dimensione.
ms.prod: xamarin
ms.assetid: 6A91EA70-268C-462C-AAAF-F8DA011403F8
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/25/2015
ms.openlocfilehash: ad20ba50b8ff0f7dcbba3e8d297b2281544a373b
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/30/2019
ms.locfileid: "68657079"
---
# <a name="xamarinforms-stacklayout"></a>StackLayout xamarin. Forms

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-layout)

[StackLayout](xref:Xamarin.Forms.StackLayout) organizza le visualizzazioni in una riga unidimensionale ("stack"), orizzontalmente o verticalmente. Viste di un `StackLayout` può essere dimensionato base allo spazio del layout usando le opzioni di layout. Il posizionamento è determinato dall'ordine con le visualizzazioni sono stati aggiunti al layout e le opzioni di layout delle visualizzazioni.

[![](stack-layout-images/layouts-sml.png "Xamarin.Forms Layouts")](stack-layout-images/layouts.png#lightbox "Xamarin.Forms Layouts")

## <a name="purpose"></a>Scopo

`StackLayout` è meno complesso altre viste. È possibile creare interfacce lineare semplice semplicemente aggiungendo visualizzazioni da un `StackLayout`e le interfacce più complesse create da loro di annidamento.

## <a name="usage--behavior"></a>L'utilizzo e comportamento

### <a name="spacing"></a>Spacing (Spaziatura)

Per impostazione predefinita, `StackLayout` aggiungerà un margine 6px tra le visualizzazioni. Ciò può essere controllata o configurata in modo non margine impostando il `Spacing` proprietà StackLayout. Di seguito viene illustrato come impostare la spaziatura e l'effetto delle opzioni di spaziatura diversa:

In XAML:

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
x:Class="LayoutSamples.StackLayoutDemo"
Title="StackLayout Demo">
  <ContentPage.Content>
    <StackLayout Spacing="10" x:Name="layout">
      <Button Text="StackLayout" VerticalOptions="Start"
        HorizontalOptions="FillAndExpand" />
      <BoxView Color="Yellow" VerticalOptions="FillAndExpand"
        HorizontalOptions="FillAndExpand" />
            <BoxView Color="Green" VerticalOptions="FillAndExpand"
        HorizontalOptions="FillAndExpand" />
            <BoxView HeightRequest="75" Color="Blue" VerticalOptions="End"
        HorizontalOptions="FillAndExpand" />
    </StackLayout>
    </ContentPage.Content>
</ContentPage>
```

In C#:

```csharp
public class StackLayoutCode : ContentPage
{
  public StackLayoutCode ()
  {
    var layout = new StackLayout ();
    var button = new Button { Text = "StackLayout", VerticalOptions = LayoutOptions.Start,
     HorizontalOptions = LayoutOptions.FillAndExpand };
    var yellowBox = new BoxView { Color = Color.Yellow, VerticalOptions = LayoutOptions.FillAndExpand, HorizontalOptions = LayoutOptions.FillAndExpand };
    var greenBox = new BoxView { Color = Color.Green, VerticalOptions = LayoutOptions.FillAndExpand, HorizontalOptions = LayoutOptions.FillAndExpand };
    var blueBox = new BoxView { Color = Color.Blue, VerticalOptions = LayoutOptions.FillAndExpand,
      HorizontalOptions = LayoutOptions.FillAndExpand, HeightRequest = 75 };

    layout.Children.Add(button);
    layout.Children.Add(yellowBox);
    layout.Children.Add(greenBox);
    layout.Children.Add(blueBox);
    layout.Spacing = 10;
    Content = layout;
  }
}
```

Spaziatura = 0:

![](stack-layout-images/spacing-zero.png "StackLayout con spaziatura = 0")

Spaziatura a dieci:

![](stack-layout-images/spacing-ten.png "StackLayout con spaziatura = 10")

### <a name="sizing"></a>Ridimensionamento

Le dimensioni di una vista in un StackLayout dipendono sia le richieste di altezza e la larghezza e le opzioni di layout. `StackLayout` impone la spaziatura interna. Nell'esempio `LayoutOption`s determinerà le viste a occupare tutto lo spazio disponibile del layout:

- **CenterAndExpand** &ndash; Centra la visualizzazione all'interno del layout e si espande in modo da occupare tutto lo spazio verrà assegnato automaticamente il layout.
- **EndAndExpand** &ndash; posiziona la visualizzazione al termine del layout (in basso o un limite più a destra) e si espande in modo da occupare tutto lo spazio verrà assegnato automaticamente il layout.
- **FillAndExpand** &ndash; posiziona la visualizzazione in modo che non ha alcun riempimento e occupa tutto lo spazio verrà assegnato automaticamente il layout.
- **StartAndExpand** &ndash; posiziona la visualizzazione all'inizio del layout e occupa tutto lo spazio fornirà l'elemento padre.

Per altre informazioni, vedere [espansione](~/xamarin-forms/user-interface/layouts/layout-options.md#expansion).

### <a name="positioning"></a>Posizionamento

Viste in un StackLayout può essere posizionate e dimensioni tramite `LayoutOptions`. Ogni vista può essere fornito `VerticalOptions` e `HorizontalOptions`, che definisce come le viste verranno posizionano in base al layout. Predefinite seguenti `LayoutOptions` disponibili:

- **Centro** &ndash; Centra la visualizzazione all'interno del layout.
- **Estremità** &ndash; posiziona la visualizzazione al termine del layout (in basso o un limite più a destra).
- **Riempire** &ndash; posiziona la visualizzazione in modo che non includa alcun riempimento.
- **Avviare** &ndash; posiziona la visualizzazione all'inizio del layout.

Il codice seguente illustra l'impostazione delle opzioni di layout:

In XAML:

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
x:Class="LayoutSamples.StackLayoutDemo"
Title="StackLayout Demo">
  <ContentPage.Content>
    <StackLayout x:Name="layout">
      <Button VerticalOptions="Start"
        HorizontalOptions="FillAndExpand" />
      <BoxView VerticalOptions="FillAndExpand"
        HorizontalOptions="FillAndExpand" />
            <BoxView VerticalOptions="FillAndExpand"
        HorizontalOptions="FillAndExpand" />
            <BoxView HeightRequest="75" VerticalOptions="End"
        HorizontalOptions="FillAndExpand" />
    </StackLayout>
    </ContentPage.Content>
</ContentPage>
```

In C#:

```csharp
public class StackLayoutCode : ContentPage
{
  public StackLayoutCode ()
  {
    var layout = new StackLayout ();
    var button = new Button { VerticalOptions = LayoutOptions.Start,
     HorizontalOptions = LayoutOptions.FillAndExpand };
    var oneBox = new BoxView { VerticalOptions = LayoutOptions.FillAndExpand, HorizontalOptions = LayoutOptions.FillAndExpand };
    var twoBox = new BoxView {  VerticalOptions = LayoutOptions.FillAndExpand, HorizontalOptions = LayoutOptions.FillAndExpand };
    var threeBox = new BoxView {  VerticalOptions = LayoutOptions.FillAndExpand, HorizontalOptions = LayoutOptions.FillAndExpand };

    layout.Children.Add(button);
    layout.Children.Add(oneBox);
    layout.Children.Add(twoBox);
    layout.Children.Add(threeBox);
    Content = layout;
  }
}
```

Per altre informazioni, vedere [allineamento](~/xamarin-forms/user-interface/layouts/layout-options.md#alignment).

## <a name="exploring-a-complex-layout"></a>Esplorazione di un Layout complesso

Ognuno dei layout presentano vantaggi e svantaggi per la creazione di layout specifico. In questa serie di articoli di layout, un'app di esempio è stata creata con lo stesso layout di pagina implementato usando tre layout diversi.

Si consideri il seguente XAML:

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
x:Class="TheBusinessTumble.StackLayoutPage"
BackgroundColor="Maroon"
Title="StackLayouts">
    <ContentPage.Content>
    <ScrollView>
      <StackLayout Spacing="0" Padding="0" BackgroundColor="Maroon">
        <BoxView HorizontalOptions="FillAndExpand" HeightRequest="100"
          VerticalOptions="Start" Color="Gray" />
        <Button BorderRadius="30" HeightRequest="60" WidthRequest="60"
          BackgroundColor="Red" HorizontalOptions="Center" VerticalOptions="Start" />
        <StackLayout HeightRequest="100" VerticalOptions="Start" HorizontalOptions="FillAndExpand" Spacing="20" BackgroundColor="Maroon">
          <Label Text="User Name" FontSize="28" HorizontalOptions="Center"
            VerticalOptions="Center" FontAttributes="Bold" />
          <Entry Text="Bio + Hashtags" TextColor="White"
            BackgroundColor="Maroon" HorizontalOptions="FillAndExpand" VerticalOptions="CenterAndExpand" />
        </StackLayout>
        <StackLayout Orientation="Horizontal" HeightRequest="50" BackgroundColor="White" Padding="5">
          <StackLayout Spacing="0" BackgroundColor="White" Orientation="Horizontal" HorizontalOptions="Start">
            <BoxView BackgroundColor="Black" WidthRequest="40" HeightRequest="40"  HorizontalOptions="StartAndExpand" VerticalOptions="Center" />
            <Label FontSize="14" TextColor="Black" Text="Accent Color" HorizontalOptions="StartAndExpand" VerticalOptions="Center" />
          </StackLayout>
          <StackLayout Spacing="0" BackgroundColor="White" Orientation="Horizontal" HorizontalOptions="EndAndExpand">
            <BoxView BackgroundColor="Maroon" WidthRequest="40" HeightRequest="40" HorizontalOptions="Start" VerticalOptions="Center" />
            <Label FontSize="14" TextColor="Black" Text="Primary Color" HorizontalOptions="StartAndExpand" VerticalOptions="Center" />
          </StackLayout>
        </StackLayout>
        <StackLayout Orientation="Horizontal">
          <Label FontSize="14" Text="Age:" TextColor="White" HorizontalOptions="Start" VerticalOptions="Center" WidthRequest="100" />
          <Entry HorizontalOptions="FillAndExpand" Text="35" TextColor="White" BackgroundColor="Maroon" />
        </StackLayout>
        <StackLayout Orientation="Horizontal">
          <Label FontSize="14" Text="Interests:" TextColor="White"
          HorizontalOptions="Start" VerticalOptions="Center" WidthRequest="100" />
          <Entry HorizontalOptions="FillAndExpand" Text="Xamarin.Forms" TextColor="White" BackgroundColor="Maroon" />
        </StackLayout>
        <StackLayout Orientation="Horizontal">
          <Label FontSize="14" Text="Ask me about:" TextColor="White"
          HorizontalOptions="Start" VerticalOptions="Center" WidthRequest="100"/>
          <Entry HorizontalOptions="FillAndExpand" Text="Xamarin, C#, .NET, Mono..." TextColor="White" BackgroundColor="Maroon" />
        </StackLayout>
      </StackLayout>
    </ScrollView>
    </ContentPage.Content>
</ContentPage>

```

Il codice precedente genera il layout seguente:

![](stack-layout-images/stack.png "StackLayout complessi")

Si noti che `StackLayouts`s sono annidate, in quanto in alcuni casi i layout di annidamento può risultare più semplice rispetto a presentare tutti gli elementi all'interno del layout stesso. Si noti inoltre che, in quanto `StackLayout` non supporta elementi sovrapposti, trovare la pagina non sono in parte il niceties layout nelle pagine per gli altri layout.



## <a name="related-links"></a>Collegamenti correlati

- [LayoutOptions](~/xamarin-forms/user-interface/layouts/layout-options.md)
- [Layout (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-layout)
- [Esempio BusinessTumble (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-businesstumble)
