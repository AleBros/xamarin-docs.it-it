---
title: Stili di dispositivo in xamarin. Forms
description: Xamarin. Forms include sei stili dinamici, noti come stili di dispositivo, nella classe Device.Styles. In questo articolo viene illustrato come utilizzare gli stili di dispositivo in un'applicazione di xamarin. Forms.
ms.prod: xamarin
ms.assetid: 7FF19ED1-0822-4238-9435-AD970317A2F8
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/17/2016
ms.openlocfilehash: 6b5d4623bb331f4bf52faa096afeacb21d6d7489
ms.sourcegitcommit: 66682dd8e93c0e4f5dee69f32b5fc5a96443e307
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/08/2018
ms.locfileid: "35245595"
---
# <a name="device-styles-in-xamarinforms"></a>Stili di dispositivo in xamarin. Forms

_Xamarin. Forms include sei stili dinamici, noti come stili di dispositivo, nella classe Device.Styles._

Il *dispositivo* stili sono:

- [`BodyStyle`](https://developer.xamarin.com/api/field/Xamarin.Forms.Device+Styles.BodyStyle/)
- [`CaptionStyle`](https://developer.xamarin.com/api/field/Xamarin.Forms.Device+Styles.CaptionStyle/)
- [`ListItemDetailTextStyle`](https://developer.xamarin.com/api/field/Xamarin.Forms.Device+Styles.ListItemDetailTextStyle/)
- [`ListItemTextStyle`](https://developer.xamarin.com/api/field/Xamarin.Forms.Device+Styles.ListItemTextStyle/)
- [`SubtitleStyle`](https://developer.xamarin.com/api/field/Xamarin.Forms.Device+Styles.SubtitleStyle/)
- [`TitleStyle`](https://developer.xamarin.com/api/field/Xamarin.Forms.Device+Styles.TitleStyle/)

Tutti i sei stili possono essere applicati solo a [ `Label` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/) istanze. Ad esempio, un `Label` che vengono visualizzati il corpo di un paragrafo potrebbe impostare il relativo [ `Style` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Style/) proprietà [ `BodyStyle` ](https://developer.xamarin.com/api/field/Xamarin.Forms.Device+Styles.BodyStyle/).

Esempio di codice seguente viene illustrato l'utilizzo di *dispositivo* stili in una pagina XAML:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms" xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml" x:Class="Styles.DeviceStylesPage" Title="Device" Icon="xaml.png">
    <ContentPage.Resources>
        <ResourceDictionary>
            <Style x:Key="myBodyStyle" TargetType="Label"
              BaseResourceKey="BodyStyle">
                <Setter Property="TextColor" Value="Accent" />
            </Style>
        </ResourceDictionary>
    </ContentPage.Resources>
    <ContentPage.Content>
        <StackLayout Padding="0,20,0,0">
            <Label Text="Title style"
              Style="{DynamicResource TitleStyle}" />
            <Label Text="Subtitle text style"
              Style="{DynamicResource SubtitleStyle}" />
            <Label Text="Body style"
              Style="{DynamicResource BodyStyle}" />
            <Label Text="Caption style"
              Style="{DynamicResource CaptionStyle}" />
            <Label Text="List item detail text style"
              Style="{DynamicResource ListItemDetailTextStyle}" />
            <Label Text="List item text style"
              Style="{DynamicResource ListItemTextStyle}" />
            <Label Text="No style" />
            <Label Text="My body style"
              Style="{StaticResource myBodyStyle}" />
        </StackLayout>
    </ContentPage.Content>
</ContentPage>
```

Gli stili di dispositivo associati all'utilizzo di `DynamicResource` estensione di markup. La natura dinamica degli stili può essere visualizzata in iOS modificando il **accessibilità** le impostazioni per la dimensione del testo. L'aspetto del *dispositivo* stili è diverso in ogni piattaforma, come illustrato nelle schermate seguenti:

![](device-images/device-styles.png "Stili di dispositivo in ciascuna piattaforma")

*Dispositivo* gli stili possono anche essere derivati da impostando il [ `BaseResourceKey` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Style.BaseResourceKey/) proprietà per il nome della chiave per lo stile del dispositivo. Nell'esempio di codice precedente, `myBodyStyle` eredita da [ `BodyStyle` ](https://developer.xamarin.com/api/field/Xamarin.Forms.Device+Styles.BodyStyle/) e imposta un colore del testo accentate. Per ulteriori informazioni sull'ereditarietà degli stili dinamici, vedere [ereditarietà degli stili dinamici](~/xamarin-forms/user-interface/styles/xaml/dynamic.md#dynamic-style-inheritance).

L'esempio di codice seguente illustra la pagina di equivalente in c#:

```csharp
public class DeviceStylesPageCS : ContentPage
{
    public DeviceStylesPageCS ()
    {
        var myBodyStyle = new Style (typeof(Label)) {
            BaseResourceKey = Device.Styles.BodyStyleKey,
            Setters = {
                new Setter {
                    Property = Label.TextColorProperty,
                    Value = Color.Accent
                }
            }
        };

        Title = "Device";
        Icon = "csharp.png";
        Padding = new Thickness (0, 20, 0, 0);

        Content = new StackLayout {
            Children = {
                new Label { Text = "Title style", Style = Device.Styles.TitleStyle },
                new Label { Text = "Subtitle style", Style = Device.Styles.SubtitleStyle },
                new Label { Text = "Body style", Style = Device.Styles.BodyStyle },
                new Label { Text = "Caption style", Style = Device.Styles.CaptionStyle },
                new Label { Text = "List item detail text style",
                  Style = Device.Styles.ListItemDetailTextStyle },
                new Label { Text = "List item text style", Style = Device.Styles.ListItemTextStyle },
                new Label { Text = "No style" },
                new Label { Text = "My body style", Style = myBodyStyle }
            }
        };
    }
}
```

Il [ `Style` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Style/) proprietà di ogni [ `Label` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/) istanza è impostata la proprietà appropriata dal [ `Devices.Styles` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Device+Styles/) classe.

## <a name="accessibility"></a>Accessibilità

Il *dispositivo* stili rispettano le preferenze di accessibilità, in modo che le dimensioni dei caratteri verrà modificato come vengono modificate le preferenze di accessibilità in ciascuna piattaforma. Pertanto, per supportare il testo accessibile, verificare che il *dispositivo* vengono utilizzati gli stili come base per tutti gli stili al testo all'interno dell'applicazione.

Le schermate seguenti illustrano gli stili di dispositivo in ogni piattaforma, con le dimensioni del carattere accessibile più piccolo:

[![](device-images/minimum-size.png "Gli stili di dispositivo di piccole dimensioni accessibile in ciascuna piattaforma")](device-images/minimum-size-large.png#lightbox "gli stili di dispositivo di piccole dimensioni accessibile in ciascuna piattaforma")

Le schermate seguenti illustrano gli stili di dispositivo in ogni piattaforma, con le dimensioni del carattere accessibile più grande:

![](device-images/maximum-size.png "Stili di periferica di grandi dimensioni accessibili in ciascuna piattaforma")

## <a name="summary"></a>Riepilogo

Xamarin. Forms include sei *dinamica* stili, noti come *dispositivo* gli stili, il [ `Devices.Styles` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Device+Styles/) classe. Tutti i sei stili possono essere applicati solo a [ `Label` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/) istanze.


## <a name="related-links"></a>Collegamenti correlati

- [Stili di testo](~/xamarin-forms/user-interface/text/styles.md)
- [Estensioni di markup XAML](~/xamarin-forms/xaml/xaml-basics/xaml-markup-extensions.md)
- [Stili dinamici (esempio)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Styles/DynamicStyles/)
- [Utilizzo degli stili (esempio)](https://developer.xamarin.com/samples/xamarin-forms/WorkingWithStyles/)
- [Device.Styles](https://developer.xamarin.com/api/type/Xamarin.Forms.Device+Styles/)
- [ResourceDictionary](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/)
- [Stile](https://developer.xamarin.com/api/type/Xamarin.Forms.Style/)
- [Metodo set](https://developer.xamarin.com/api/type/Xamarin.Forms.Setter/)
