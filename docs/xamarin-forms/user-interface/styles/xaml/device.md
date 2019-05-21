---
title: Stili di dispositivo in xamarin. Forms
description: Xamarin. Forms include sei stili dinamici, noti come gli stili di dispositivo, nella classe Device.Styles. Questo articolo illustra come usare gli stili di dispositivo in un'applicazione xamarin. Forms.
ms.prod: xamarin
ms.assetid: 7FF19ED1-0822-4238-9435-AD970317A2F8
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/17/2016
ms.openlocfilehash: 252f3271c7247f7070df66712035938be651e7f4
ms.sourcegitcommit: b23a107b0fe3d2f814ae35b52a5855b6ce2a3513
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/20/2019
ms.locfileid: "65924796"
---
# <a name="device-styles-in-xamarinforms"></a>Stili di dispositivo in xamarin. Forms

[![Scaricare l'esempio](~/media/shared/download.png) scaricare l'esempio](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Styles/DynamicStyles/)

_Xamarin. Forms include sei stili dinamici, noti come gli stili di dispositivo, nella classe Device.Styles._

Il *dispositivo* gli stili sono:

- [`BodyStyle`](xref:Xamarin.Forms.Device.Styles.BodyStyle)
- [`CaptionStyle`](xref:Xamarin.Forms.Device.Styles.CaptionStyle)
- [`ListItemDetailTextStyle`](xref:Xamarin.Forms.Device.Styles.ListItemDetailTextStyle)
- [`ListItemTextStyle`](xref:Xamarin.Forms.Device.Styles.ListItemTextStyle)
- [`SubtitleStyle`](xref:Xamarin.Forms.Device.Styles.SubtitleStyle)
- [`TitleStyle`](xref:Xamarin.Forms.Device.Styles.TitleStyle)

Tutti i sei stili possono essere applicati solo a [ `Label` ](xref:Xamarin.Forms.Label) istanze. Ad esempio, un `Label` che visualizza il corpo di un paragrafo D97B relativi [ `Style` ](xref:Xamarin.Forms.NavigableElement.Style) proprietà [ `BodyStyle` ](xref:Xamarin.Forms.Device.Styles.BodyStyle).

Esempio di codice seguente viene illustrato come utilizzare il *dispositivo* stili in una pagina XAML:

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

Gli stili di dispositivo associati all'uso di `DynamicResource` estensione di markup. La natura dinamica degli stili può essere visualizzata nel iOS modificando i **accessibilità** impostazioni per le dimensioni del testo. L'aspetto del *dispositivo* stili è diverso in ogni piattaforma, come illustrato negli screenshot seguenti:

![](device-images/device-styles.png "Stili di dispositivo in ogni piattaforma")

*Periferica* stili possono anche essere derivati dalle impostando il [ `BaseResourceKey` ](xref:Xamarin.Forms.Style.BaseResourceKey) proprietà per il nome della chiave per lo stile di dispositivo. Nell'esempio di codice precedente, `myBodyStyle` eredita da [ `BodyStyle` ](xref:Xamarin.Forms.Device.Styles.BodyStyle) e imposta un colore del testo accentate. Per altre informazioni sull'ereditarietà degli stili dinamici, vedere [ereditarietà degli stili dinamici](~/xamarin-forms/user-interface/styles/xaml/dynamic.md#dynamic-style-inheritance).

Esempio di codice seguente illustra la pagina equivalente nel linguaggio C#:

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

Il [ `Style` ](xref:Xamarin.Forms.NavigableElement.Style) proprietà della ognuno [ `Label` ](xref:Xamarin.Forms.Label) istanza è impostata sulla proprietà appropriata dal [ `Devices.Styles` ](xref:Xamarin.Forms.Device.Styles) classe.

## <a name="accessibility"></a>Accessibilità

Il *dispositivo* stili rispettano le preferenze di accessibilità, in modo che le dimensioni dei caratteri e verrà modificato le preferenze di accessibilità vengono modificate in ogni piattaforma. Di conseguenza, per supportare il testo di accessibilità, assicurarsi che il *dispositivo* stili vengono usati come base per tutti gli stili di testo all'interno dell'applicazione.

Gli screenshot seguenti illustrano gli stili di dispositivo in ogni piattaforma, con le dimensioni del carattere accessibili più piccolo:

[![](device-images/minimum-size.png "Gli stili di dispositivo di piccole dimensioni accessibile in ogni piattaforma")](device-images/minimum-size-large.png#lightbox "gli stili di dispositivo di piccole dimensioni accessibile in ogni piattaforma")

Gli screenshot seguenti illustrano gli stili di dispositivo in ogni piattaforma, con le dimensioni massime del carattere accessibile:

![](device-images/maximum-size.png "Stili di dispositivo di grandi dimensioni accessibile in ogni piattaforma")

## <a name="related-links"></a>Collegamenti correlati

- [Stili del testo](~/xamarin-forms/user-interface/text/styles.md)
- [Estensioni di markup XAML](~/xamarin-forms/xaml/xaml-basics/xaml-markup-extensions.md)
- [Stili dinamici (esempio)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Styles/DynamicStyles/)
- [Utilizzo di stili (esempio)](https://developer.xamarin.com/samples/xamarin-forms/WorkingWithStyles/)
- [Device.Styles](xref:Xamarin.Forms.Device.Styles)
- [ResourceDictionary](xref:Xamarin.Forms.ResourceDictionary)
- [Stile di visualizzazione](xref:Xamarin.Forms.Style)
- [Setter](xref:Xamarin.Forms.Setter)
