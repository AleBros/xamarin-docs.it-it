---
title: " Xamarin.Forms helper della piattaforma dual screen" Descrizione: "questa guida illustra come usare Xamarin.Forms la classe DualScreenHelper per ottimizzare l'esperienza di app per dispositivi a doppio schermo, ad esempio Surface Duo e Surface neo".
ms. prod: Novell MS. AssetID: 5aa184c2-5611-427d-85c7-1c56486c3e1b ms. Technology: Novell-Forms Author: davidortinau ms. Author: daortin ms. Date: 02/08/2020 no-loc: [ Xamarin.Forms , Xamarin.Essentials ]
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
