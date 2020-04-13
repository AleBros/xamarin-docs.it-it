---
title: Xamarin.Forms helper di piattaforma dual-screen
description: Questa guida illustra come usare la classe DualScreenHelper di Xamarin.Forms per ottimizzare l'esperienza delle app per dispositivi con doppio schermo, ad esempio Surface Duo e Surface Neo.
ms.prod: xamarin
ms.assetid: 5aa184c2-5611-427d-85c7-1c56486c3e1b
ms.technology: xamarin-forms
author: davidortinau
ms.author: daortin
ms.date: 02/08/2020
ms.openlocfilehash: 9ab6106b8b92660d416e8a22cc3b1bdf1b41c5cf
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2020
ms.locfileid: "80628273"
---
# <a name="xamarinforms-dual-screen-platform-helpers"></a>Xamarin.Forms helper di piattaforma dual-screen

![](~/media/shared/preview.png "This API is currently pre-release")

[![Scarica](~/media/shared/download.png) l'esempio Scarica l'esempioDownload Sample Download the sample](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-dualscreendemos/)

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
