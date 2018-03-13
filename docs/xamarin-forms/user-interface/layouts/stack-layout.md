---
title: StackLayout
description: Utilizzare StackLayout per presentare le raccolte di viste in una dimensione.
ms.topic: article
ms.prod: xamarin
ms.assetid: 6A91EA70-268C-462C-AAAF-F8DA011403F8
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/25/2015
ms.openlocfilehash: 14982fc5cb3e05243c819cbc8b37349d872bd24c
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/09/2018
---
# <a name="stacklayout"></a>StackLayout

`StackLayout` Consente di organizzare le visualizzazioni in una riga unidimensionale ("stack"), orizzontalmente o verticalmente. Viste di un `StackLayout` può essere dimensionato in base allo spazio del layout utilizzando le opzioni di layout. Posizionamento è determinato dall'ordine viste sono state aggiunte per il layout e le opzioni di layout delle viste.

[![](stack-layout-images/layouts-sml.png "Xamarin.Forms Layouts")](stack-layout-images/layouts.png#lightbox "Xamarin.Forms Layouts")

## <a name="purpose"></a>Scopo

`StackLayout` è meno complesso altre visualizzazioni. È possibile creare interfacce lineare semplice semplicemente aggiungendo le visualizzazioni per un `StackLayout`e le interfacce più complesse create nidificate.

## <a name="usage--behavior"></a>Utilizzo e comportamento

### <a name="spacing"></a>Spacing (Spaziatura)

Per impostazione predefinita, `StackLayout` aggiungerà un margine 6px tra le visualizzazioni. Questo può essere controllato o impostato come non margine impostando il `Spacing` proprietà StackLayout. Di seguito viene illustrato come impostare la spaziatura e l'effetto delle opzioni di spaziatura diversi:

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

Spaziatura di dieci:

![](stack-layout-images/spacing-ten.png "StackLayout con spaziatura = 10")

### <a name="sizing"></a>Ridimensionamento

Le dimensioni di una vista in un StackLayout dipendono l'altezza e larghezza di richieste e le opzioni di layout. `StackLayout` Consente di applicare la spaziatura interna. Le operazioni seguenti `LayoutOption`s causerà viste occupare tutto lo spazio disponibile dal layout:

- **CenterAndExpand** &ndash; Centra la visualizzazione all'interno del layout e si espande in modo da occupare tutto lo spazio il layout verrà assegnato automaticamente.
- **EndAndExpand** &ndash; posiziona la vista alla fine del layout (in basso o limite più a destra) e si espande in modo da occupare tutto lo spazio il layout verrà assegnato automaticamente.
- **FillAndExpand** &ndash; posiziona la vista in modo che non ha alcun riempimento e occupa tutto lo spazio il layout verrà assegnato automaticamente.
- **StartAndExpand** &ndash; posiziona la vista all'inizio del layout e occupa tutto lo spazio fornirà l'elemento padre.

Per ulteriori informazioni, vedere [espansione](~/xamarin-forms/user-interface/layouts/layout-options.md#expansion).

### <a name="positioning"></a>Posizionamento

Visualizzazioni in un StackLayout può essere posizionate e dimensioni tramite `LayoutOptions`. È possibile assegnare ogni visualizzazione `VerticalOptions` e `HorizontalOptions`, che definisce come le viste verranno posizionano in base il layout. I seguenti predefiniti `LayoutOptions` sono disponibili:

- **Center** &ndash; Centra la visualizzazione all'interno del layout.
- **Fine** &ndash; posiziona la vista alla fine del layout (in basso o limite più a destra).
- **Riempimento** &ndash; posiziona la vista in modo che non disponga di alcun riempimento.
- **Avviare** &ndash; posiziona la vista all'inizio del layout.

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

Per ulteriori informazioni, vedere [allineamento](~/xamarin-forms/user-interface/layouts/layout-options.md#alignment).

## <a name="exploring-a-complex-layout"></a>Esplorazione di un Layout complesso

Ogni layout di disporre di vantaggi e svantaggi per la creazione di un layout specifico. Questa serie di articoli di layout, un'app di esempio è stata creata con lo stesso layout di pagina implementato utilizzando tre layout diversi.

Si consideri il codice XAML seguente:

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

Il codice precedente produce lo schema seguente:

![](stack-layout-images/stack.png "StackLayout complessi")

Si noti che, a causa della differenza nella modalità di rendering dei pulsanti da Windows Phone, alcuni dei cerchi sono stati sostituiti da boxviews nella schermata di Windows Phone.

Si noti che `StackLayouts`s sono annidate, perché in alcuni casi la nidificazione di layout può essere più semplice rispetto a tutti gli elementi all'interno del layout stesso presentazione. Si noti inoltre che, in quanto `StackLayout` non supporta elementi sovrapposti, la pagina non sono alcuni niceties il layout presenti nelle pagine per gli altri layout.



## <a name="related-links"></a>Collegamenti correlati

- [LayoutOptions](~/xamarin-forms/user-interface/layouts/layout-options.md)
- [Layout (esempio)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Layout/)
- [Esempio BusinessTumble (esempio)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/BusinessTumble/)
