---
title: Xamarin.Formshelper della piattaforma dual screen
description: Questa guida illustra come usare Xamarin.Forms la classe DualScreenHelper per ottimizzare l'esperienza delle app per dispositivi a doppio schermo, ad esempio Surface Duo e Surface neo.
ms.prod: ''
ms.assetid: ''
ms.technology: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: d9daf5a24c0dcfd07d529955c411259f4c1359df
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/28/2020
ms.locfileid: "84138944"
---
# <a name="xamarinforms-dual-screen-platform-helpers"></a>Xamarin.Formshelper della piattaforma dual screen

![](~/media/shared/preview.png "This API is currently pre-release")

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-dualscreendemos/)

La classe `DualScreenHelper` può essere usata per rilevare se un dispositivo supporta la modalità PIP (Picture-in-Picture) e quindi consente di aprire un elemento `ContentPage` come immagine nella finestra immagine. In un dispositivo Neo la pagina verrà aperta nella Wonder Bar in modalità composizione.

## <a name="properties"></a>Proprietà

- `HasCompactModeSupport`: usata per verificare se la piattaforma supporta l'apertura di un elemento `ContentPage` in CompactMode.
- `OpenCompactMode`: apre un elemento `ContentPage` in CompactMode, se supportato dalla piattaforma.

## <a name="example"></a>Esempio

```csharp
async void OpenCompactWindowClicked(object sender, EventArgs e)
{
    if (!DualScreenHelper.HasCompactModeSupport())
    {
        await DisplayAlert("Unsupported", "This platform doesn't support this feature", "Ok");
        return;
    }

    ContentPage page = new ContentPage() { BackgroundColor = Color.Purple };

    var button = new Button()
    {
        Text = "Close this Window"
    };

    var layout = new StackLayout()
    {
        Children =
        {
            new Label(){ Text = "Welcome to your Compact Mode Window" },
            button
        },
        BackgroundColor = Color.Yellow,
        HorizontalOptions = LayoutOptions.Fill,
        VerticalOptions = LayoutOptions.Fill
    };

    page.Content = new ScrollView() { Content = layout };
    var args = await DualScreenHelper.OpenCompactMode(page);

    button.Command = new Command(async () =>
    {
        await args.CloseAsync();
    });
}
```
