---
title: Controllo DualScreenHelper di Xamarin.Forms
description: Questa guida illustra come usare la classe DualScreenHelper di Xamarin.Forms per ottimizzare l'esperienza delle app per dispositivi con doppio schermo, ad esempio Surface Duo e Surface Neo.
ms.prod: xamarin
ms.assetid: 5aa184c2-5611-427d-85c7-1c56486c3e1b
ms.technology: xamarin-forms
author: davidortinau
ms.author: daortin
ms.date: 02/08/2020
ms.openlocfilehash: 26e6389dc7effd52fad8307e1e3191fc4b760fe6
ms.sourcegitcommit: 524fc148bad17272bda83c50775771daa45bfd7e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/20/2020
ms.locfileid: "77480574"
---
# <a name="xamarinforms-dualscreenhelper"></a>Controllo DualScreenHelper di Xamarin.Forms

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://github.com/xamarin/xamarin-forms-samples/UserInterface/DualScreenDemos)

La classe `DualScreenHelper` può essere usata per rilevare se un dispositivo supporta la modalità PIP (Picture-in-Picture) e quindi consente di aprire un elemento `ContentPage` come immagine nella finestra immagine. In un dispositivo Neo la pagina verrà aperta nella Wonder Bar in modalità composizione.

## <a name="properties"></a>Proprietà

- `HasCompactModeSupport`: usata per verificare se la piattaforma supporta l'apertura di un elemento `ContentPage` in CompactMode.
- `OpenCompactMode`: apre un elemento `ContentPage` in CompactMode, se supportato dalla piattaforma.

## <a name="example"></a>Esempio

```csharp
async void OpenCompactWindowClicked(object sender, EventArgs e)
{
    if(!DualScreenHelper.HasCompactModeSupport())
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
