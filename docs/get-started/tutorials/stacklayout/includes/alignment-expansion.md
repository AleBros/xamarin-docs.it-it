---
ms.openlocfilehash: f87367ce7bc7e364147d087cdd126efaa6bf910c
ms.sourcegitcommit: 3f0e4f10e5def19122588bb05f26ab2baa9df6eb
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/23/2020
ms.locfileid: "67560003"
---
Le dimensioni e la posizione delle viste figlio all'interno di un elemento [`StackLayout`](xref:Xamarin.Forms.StackLayout) dipende dai valori delle proprietà [`HeightRequest`](xref:Xamarin.Forms.VisualElement.HeightRequest) e [`WidthRequest`](xref:Xamarin.Forms.VisualElement.WidthRequest) delle viste figlio e dai valori delle proprietà [`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) e [`VerticalOptions`](xref:Xamarin.Forms.View.VerticalOptions).

Le proprietà [`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) e [`VerticalOptions`](xref:Xamarin.Forms.View.VerticalOptions) possono essere impostate sui campi dello struct [`LayoutOptions`](xref:Xamarin.Forms.LayoutOptions) che incapsula due preferenze di layout:

- **Allineamento**: l'allineamento preferito della vista figlio che determina la sua posizione e dimensioni nel layout padre.
- **Espansione**: indica se la vista figlio deve usare spazio aggiuntivo quando è disponibile ed è utilizzata solo da un elemento [`StackLayout`](xref:Xamarin.Forms.StackLayout).

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. In **MainPage.xaml** modificare la dichiarazione [`StackLayout`](xref:Xamarin.Forms.StackLayout) per impostare le opzioni di allineamento ed espansione per ogni elemento [`Label`](xref:Xamarin.Forms.Label):

    ```xaml
    <StackLayout Margin="20,35,20,25">
        <Label Text="Start"
               HorizontalOptions="Start"
               BackgroundColor="Gray" />
        <Label Text="Center"
               HorizontalOptions="Center"
               BackgroundColor="Gray" />
        <Label Text="End"
               HorizontalOptions="End"
               BackgroundColor="Gray" />
        <Label Text="Fill"
               HorizontalOptions="Fill"
               BackgroundColor="Gray" />
        <Label Text="StartAndExpand"
               VerticalOptions="StartAndExpand"
               BackgroundColor="Gray" />
        <Label Text="CenterAndExpand"
               VerticalOptions="CenterAndExpand"
               BackgroundColor="Gray" />
        <Label Text="EndAndExpand"
               VerticalOptions="EndAndExpand"
               BackgroundColor="Gray" />
        <Label Text="FillAndExpand"
               VerticalOptions="FillAndExpand"
               BackgroundColor="Gray" />
    </StackLayout>
    ```

    Questo codice imposta le preferenze di allineamento sulle prime quattro istanze [`Label`](xref:Xamarin.Forms.Label) e le preferenze di espansione sulle quattro istanze `Label` finali. I campi [`Start`](xref:Xamarin.Forms.LayoutOptions.Start), [`Center`](xref:Xamarin.Forms.LayoutOptions.Center), [`End`](xref:Xamarin.Forms.LayoutOptions.End) e [`Fill`](xref:Xamarin.Forms.LayoutOptions.Fill) sono usati per definire l'allineamento delle istanze [`Label`](xref:Xamarin.Forms.Label) all'interno dell'elemento [`StackLayout`](xref:Xamarin.Forms.StackLayout) padre. I campi [`StartAndExpand`](xref:Xamarin.Forms.LayoutOptions.StartAndExpand), [`CenterAndExpand`](xref:Xamarin.Forms.LayoutOptions.CenterAndExpand), [`EndAndExpand`](xref:Xamarin.Forms.LayoutOptions.EndAndExpand) e [`FillAndExpand`](xref:Xamarin.Forms.LayoutOptions.FillAndExpand) sono usati per definire le preferenza di allineamento e se la vista occuperà più spazio quando è disponibile nell'elemento `StackLayout` padre.

    > [!NOTE]
    > Il valore predefinito delle proprietà [`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) e [`VerticalOptions`](xref:Xamarin.Forms.View.VerticalOptions) di una vista è [`Fill`](xref:Xamarin.Forms.LayoutOptions.Fill).

1. Nella barra degli strumenti di Visual Studio premere il pulsante **Avvia**, ovvero il pulsante a forma di triangolo simile a un pulsante di riproduzione, per avviare l'applicazione all'interno del simulatore iOS remoto o dell'emulatore Android prescelto:

    [![Screenshot delle visualizzazioni figlio in un controllo StackLayout, con opzioni di allineamento ed espansione impostate, in iOS e Android](../images/alignment-expansion.png "StackLayout contenente istanze di etichette, con allineamento ed espansione impostati")](../images/alignment-expansion-large.png#lightbox "StackLayout contenente istanze di etichette, con allineamento ed espansione impostati")

    Un elemento [`StackLayout`](xref:Xamarin.Forms.StackLayout) rispetta solo le preferenze di allineamento nelle viste figlio che hanno la direzione opposta all'orientamento di `StackLayout`. Pertanto, le viste figlio [`Label`](xref:Xamarin.Forms.Label) all'interno dell'elemento `StackLayout` orientato verticalmente impostano le loro proprietà [`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) su uno dei campi di allineamento:

    - [`Start`](xref:Xamarin.Forms.LayoutOptions.Start), che posiziona [`Label`](xref:Xamarin.Forms.Label) sul lato sinistro di [`StackLayout`](xref:Xamarin.Forms.StackLayout).
    - [`Center`](xref:Xamarin.Forms.LayoutOptions.Center), che centra [`Label`](xref:Xamarin.Forms.Label) in [`StackLayout`](xref:Xamarin.Forms.StackLayout).
    - [`End`](xref:Xamarin.Forms.LayoutOptions.End), che posiziona [`Label`](xref:Xamarin.Forms.Label) sul lato destro di [`StackLayout`](xref:Xamarin.Forms.StackLayout).
    - [`Fill`](xref:Xamarin.Forms.LayoutOptions.Fill), con cui [`Label`](xref:Xamarin.Forms.Label) riempie la larghezza di [`StackLayout`](xref:Xamarin.Forms.StackLayout).

    Un elemento [`StackLayout`](xref:Xamarin.Forms.StackLayout) può espandere le viste figlio solo nella direzione del proprio orientamento. Pertanto, l'elemento `StackLayout` orientato verticalmente può espandere le viste figlio [`Label`](xref:Xamarin.Forms.Label) che impostano le loro proprietà [`VerticalOptions`](xref:Xamarin.Forms.View.VerticalOptions) su uno dei campi di espansione. Ciò significa che, per l'allineamento verticale, ogni `Label` occupa la stessa quantità di spazio all'interno dell'elemento `StackLayout`. Tuttavia solo l'elemento `Label` finale che imposta la sua proprietà [`VerticalOptions`](xref:Xamarin.Forms.View.VerticalOptions) su [`FillAndExpand`](xref:Xamarin.Forms.LayoutOptions.FillAndExpand) ha una dimensione diversa.

    > [!IMPORTANT]
    > Quando tutto lo spazio in un elemento [`StackLayout`](xref:Xamarin.Forms.StackLayout) è utilizzato, le preferenze di espansione non hanno alcun effetto.

    Per altre informazioni sull'allineamento e l'espansione, vedere [Layout Options in Xamarin.Forms](~/xamarin-forms/user-interface/layouts/layout-options.md) (Opzioni di Layout in Xamarin.Forms).

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

1. In **MainPage.xaml** modificare la dichiarazione [`StackLayout`](xref:Xamarin.Forms.StackLayout) per impostare le opzioni di allineamento ed espansione per ogni elemento [`Label`](xref:Xamarin.Forms.Label):

    ```xaml
    <StackLayout Margin="20,35,20,25">
        <Label Text="Start"
               HorizontalOptions="Start"
               BackgroundColor="Gray" />
        <Label Text="Center"
               HorizontalOptions="Center"
               BackgroundColor="Gray" />
        <Label Text="End"
               HorizontalOptions="End"
               BackgroundColor="Gray" />
        <Label Text="Fill"
               HorizontalOptions="Fill"
               BackgroundColor="Gray" />
        <Label Text="StartAndExpand"
               VerticalOptions="StartAndExpand"
               BackgroundColor="Gray" />
        <Label Text="CenterAndExpand"
               VerticalOptions="CenterAndExpand"
               BackgroundColor="Gray" />
        <Label Text="EndAndExpand"
               VerticalOptions="EndAndExpand"
               BackgroundColor="Gray" />
        <Label Text="FillAndExpand"
               VerticalOptions="FillAndExpand"
               BackgroundColor="Gray" />
    </StackLayout>
    ```

    Questo codice imposta le preferenze di allineamento sulle prime quattro istanze [`Label`](xref:Xamarin.Forms.Label) e le preferenze di espansione sulle quattro istanze `Label` finali. I campi [`Start`](xref:Xamarin.Forms.LayoutOptions.Start), [`Center`](xref:Xamarin.Forms.LayoutOptions.Center), [`End`](xref:Xamarin.Forms.LayoutOptions.End) e [`Fill`](xref:Xamarin.Forms.LayoutOptions.Fill) sono usati per definire l'allineamento delle istanze [`Label`](xref:Xamarin.Forms.Label) all'interno dell'elemento [`StackLayout`](xref:Xamarin.Forms.StackLayout) padre. I campi [`StartAndExpand`](xref:Xamarin.Forms.LayoutOptions.StartAndExpand), [`CenterAndExpand`](xref:Xamarin.Forms.LayoutOptions.CenterAndExpand), [`EndAndExpand`](xref:Xamarin.Forms.LayoutOptions.EndAndExpand) e [`FillAndExpand`](xref:Xamarin.Forms.LayoutOptions.FillAndExpand) sono usati per definire le preferenza di allineamento e se la vista occuperà più spazio quando è disponibile nell'elemento `StackLayout` padre.

    > [!NOTE]
    > Il valore predefinito delle proprietà [`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) e [`VerticalOptions`](xref:Xamarin.Forms.View.VerticalOptions) di una vista è [`Fill`](xref:Xamarin.Forms.LayoutOptions.Fill).

1. Nella barra degli strumenti di Visual Studio per Mac premere il pulsante **Avvia**, ovvero il pulsante a forma di triangolo simile a un pulsante di riproduzione, per avviare l'applicazione all'interno del simulatore iOS o dell'emulatore Android prescelto:

    [![Screenshot delle visualizzazioni figlio in un controllo StackLayout, con opzioni di allineamento ed espansione impostate, in iOS e Android](../images/alignment-expansion.png "StackLayout contenente istanze di etichette, con allineamento ed espansione impostati")](../images/alignment-expansion-large.png#lightbox "StackLayout contenente istanze di etichette, con allineamento ed espansione impostati")

    Un elemento [`StackLayout`](xref:Xamarin.Forms.StackLayout) rispetta solo le preferenze di allineamento nelle viste figlio che hanno la direzione opposta all'orientamento di `StackLayout`. Pertanto, le viste figlio [`Label`](xref:Xamarin.Forms.Label) all'interno dell'elemento `StackLayout` orientato verticalmente impostano le loro proprietà [`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) su uno dei campi di allineamento:

    - [`Start`](xref:Xamarin.Forms.LayoutOptions.Start), che posiziona [`Label`](xref:Xamarin.Forms.Label) sul lato sinistro di [`StackLayout`](xref:Xamarin.Forms.StackLayout).
    - [`Center`](xref:Xamarin.Forms.LayoutOptions.Center), che centra [`Label`](xref:Xamarin.Forms.Label) in [`StackLayout`](xref:Xamarin.Forms.StackLayout).
    - [`End`](xref:Xamarin.Forms.LayoutOptions.End), che posiziona [`Label`](xref:Xamarin.Forms.Label) sul lato destro di [`StackLayout`](xref:Xamarin.Forms.StackLayout).
    - [`Fill`](xref:Xamarin.Forms.LayoutOptions.Fill), con cui [`Label`](xref:Xamarin.Forms.Label) riempie la larghezza di [`StackLayout`](xref:Xamarin.Forms.StackLayout).

    Un elemento [`StackLayout`](xref:Xamarin.Forms.StackLayout) può espandere le viste figlio solo nella direzione del proprio orientamento. Pertanto, l'elemento `StackLayout` orientato verticalmente può espandere le viste figlio [`Label`](xref:Xamarin.Forms.Label) che impostano le loro proprietà [`VerticalOptions`](xref:Xamarin.Forms.View.VerticalOptions) su uno dei campi di espansione. Ciò significa che, per l'allineamento verticale, ogni `Label` occupa la stessa quantità di spazio all'interno dell'elemento `StackLayout`. Tuttavia solo l'elemento `Label` finale che imposta la sua proprietà [`VerticalOptions`](xref:Xamarin.Forms.View.VerticalOptions) su [`FillAndExpand`](xref:Xamarin.Forms.LayoutOptions.FillAndExpand) ha una dimensione diversa.

    > [!IMPORTANT]
    > Quando tutto lo spazio in un elemento [`StackLayout`](xref:Xamarin.Forms.StackLayout) è utilizzato, le preferenze di espansione non hanno alcun effetto.

    Per altre informazioni sull'allineamento e l'espansione, vedere [Layout Options in Xamarin.Forms](~/xamarin-forms/user-interface/layouts/layout-options.md) (Opzioni di Layout in Xamarin.Forms).

-----

> [!div class="nextstepaction"]
> [Si è verificato un problema](https://github.com/MicrosoftDocs/xamarin-docs/issues/new?title=StackLayout+Tutorial+Step+3+Feedback&template=tutorial_template.md)
