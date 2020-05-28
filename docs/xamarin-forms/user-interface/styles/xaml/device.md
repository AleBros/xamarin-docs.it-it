---
title: Stili del dispositivo inXamarin.Forms
description: Xamarin.Formsinclude sei stili dinamici, noti come stili di dispositivo, nella classe Device. Styles. Questo articolo illustra come utilizzare gli stili del dispositivo in un' Xamarin.Forms applicazione.
ms.prod: ''
ms.assetid: ''
ms.technology: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: b835847fea39e1c2f968e7b81fb9d22f68ea461c
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/28/2020
ms.locfileid: "84140101"
---
# <a name="device-styles-in-xamarinforms"></a>Stili del dispositivo inXamarin.Forms

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-styles-dynamicstyles)

_Novell. Forms include sei stili dinamici, noti come stili del dispositivo, nella classe Device. Styles._

Gli stili del *dispositivo* sono:

- [`BodyStyle`](xref:Xamarin.Forms.Device.Styles.BodyStyle)
- [`CaptionStyle`](xref:Xamarin.Forms.Device.Styles.CaptionStyle)
- [`ListItemDetailTextStyle`](xref:Xamarin.Forms.Device.Styles.ListItemDetailTextStyle)
- [`ListItemTextStyle`](xref:Xamarin.Forms.Device.Styles.ListItemTextStyle)
- [`SubtitleStyle`](xref:Xamarin.Forms.Device.Styles.SubtitleStyle)
- [`TitleStyle`](xref:Xamarin.Forms.Device.Styles.TitleStyle)

Tutti i sei stili possono essere applicati solo alle [`Label`](xref:Xamarin.Forms.Label) istanze. Ad esempio, un oggetto `Label` che Visualizza il corpo di un paragrafo potrebbe impostare la relativa [`Style`](xref:Xamarin.Forms.NavigableElement.Style) proprietà su [`BodyStyle`](xref:Xamarin.Forms.Device.Styles.BodyStyle) .

L'esempio di codice seguente illustra l'uso degli stili del *dispositivo* in una pagina XAML:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms" xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml" x:Class="Styles.DeviceStylesPage" Title="Device" IconImageSource="xaml.png">
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

Gli stili del dispositivo sono associati all'uso dell' `DynamicResource` estensione di markup. La natura dinamica degli stili può essere visualizzata in iOS cambiando le impostazioni di **accessibilità** per le dimensioni del testo. L'aspetto degli stili del *dispositivo* è diverso in ogni piattaforma, come illustrato nelle schermate seguenti:

![](device-images/device-styles.png "Device Styles on Each Platform")

Gli stili del *dispositivo* possono anche essere derivati da impostando la [`BaseResourceKey`](xref:Xamarin.Forms.Style.BaseResourceKey) proprietà sul nome della chiave per lo stile del dispositivo. Nell'esempio di codice precedente `myBodyStyle` eredita da [`BodyStyle`](xref:Xamarin.Forms.Device.Styles.BodyStyle) e imposta un colore del testo accentato. Per ulteriori informazioni sull'ereditarietà dinamica dello stile, vedere [ereditarietà dello stile dinamico](~/xamarin-forms/user-interface/styles/xaml/dynamic.md#dynamic-style-inheritance).

Nell'esempio di codice seguente viene illustrata la pagina equivalente in C#:

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
        IconImageSource = "csharp.png";
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

La [`Style`](xref:Xamarin.Forms.NavigableElement.Style) proprietà di ogni [`Label`](xref:Xamarin.Forms.Label) istanza viene impostata sulla proprietà appropriata dalla [`Devices.Styles`](xref:Xamarin.Forms.Device.Styles) classe.

## <a name="accessibility"></a>Accessibilità

Gli stili del *dispositivo* rispettano le preferenze di accessibilità, quindi le dimensioni dei caratteri cambiano in base alle preferenze di accessibilità modificate in ogni piattaforma. Pertanto, per supportare il testo accessibile, assicurarsi che gli stili del *dispositivo* vengano usati come base per tutti gli stili di testo all'interno dell'applicazione.

Gli screenshot seguenti illustrano gli stili del dispositivo in ogni piattaforma, con le dimensioni dei caratteri accessibili più piccole:

[![](device-images/minimum-size.png "Accessible Small Device Styles on Each Platform")](device-images/minimum-size-large.png#lightbox "Accessible Small Device Styles on Each Platform")

Gli screenshot seguenti illustrano gli stili del dispositivo in ogni piattaforma, con le dimensioni dei caratteri accessibili più grandi:

![](device-images/maximum-size.png "Accessible Large Device Styles on Each Platform")

## <a name="related-links"></a>Collegamenti correlati

- [Stili di testo](~/xamarin-forms/user-interface/text/styles.md)
- [Estensioni di markup XAML](~/xamarin-forms/xaml/xaml-basics/xaml-markup-extensions.md)
- [Stili dinamici (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-styles-dynamicstyles)
- [Utilizzo degli stili (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithstyles)
- [Device. Styles](xref:Xamarin.Forms.Device.Styles)
- [ResourceDictionary](xref:Xamarin.Forms.ResourceDictionary)
- [Stile](xref:Xamarin.Forms.Style)
- [Setter](xref:Xamarin.Forms.Setter)
