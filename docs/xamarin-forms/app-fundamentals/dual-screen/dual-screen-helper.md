---
title: Controllo DualScreenHelper di Xamarin.Forms
description: Questa guida illustra come usare il controllo DualScreenHelper di Xamarin.Forms per ottimizzare l'esperienza delle app per dispositivi con doppio schermo, ad esempio Surface Duo e Surface Neo.
ms.prod: xamarin
ms.assetid: 5aa184c2-5611-427d-85c7-1c56486c3e1b
ms.technology: xamarin-forms
author: davidortinau
ms.author: daortin
ms.date: 02/08/2020
ms.openlocfilehash: b06e105560de635a21b42e8366bb47842372cf6a
ms.sourcegitcommit: 07941cf9704ff88cf4087de5ebdea623ff54edb1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/11/2020
ms.locfileid: "77145545"
---
# <a name="xamarinforms-dualscreenhelper"></a>Controllo DualScreenHelper di Xamarin.Forms
[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://github.com/xamarin/xamarin-forms-samples/UserInterface/DualScreenDemos)
_DualScreenHelper può essere usato per rilevare se un dispositivo supporta la modalità PIP (Picture-in-Picture) e quindi consente di aprire un elemento ContentPage come immagine nella finestra immagine. Se eseguito in un dispositivo Neo, la pagina verrà aperta nella Wonder Bar in modalità composizione._
## <a name="properties"></a>Proprietà
- `HasCompactModeSupport`: usata per verificare se la piattaforma supporta l'apertura di un elemento ContentPage in CompactMode.
- `OpenCompactMode`: se supportato dalla piattaforma, verrà aperto un elemento ContentPage in CompactMode.
## <a name="example-usage"></a>Esempio di utilizzo
```c#
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