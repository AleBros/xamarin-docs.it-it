---
ms.openlocfilehash: a7c2aa15521b89e4930746dc5421ce67fa26b2b9
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/09/2020
ms.locfileid: "67560002"
---
# <a name="visual-studio"></a>[Visual Studio](#tab/vswin)

1. In **MainPage.xaml** modificare la dichiarazione [`StackLayout`](xref:Xamarin.Forms.StackLayout) per allineare gli elementi figlio orizzontalmente anziché verticalmente:

    ```xaml
    <StackLayout Margin="20,35,20,25"
                 Orientation="Horizontal">
        <Label Text="The StackLayout has its Margin property set, to control the rendering position of the StackLayout." />
        <Label Text="The Padding property can be set to specify the distance between the StackLayout and its children." />
        <Label Text="The Spacing property can be set to specify the distance between views in the StackLayout." />
    </StackLayout>
    ```

    Questo codice imposta la proprietà [`Orientation`](xref:Xamarin.Forms.StackLayout.Orientation) su [`Horizontal`](xref:Xamarin.Forms.StackOrientation.Horizontal).

1. Nella barra degli strumenti di Visual Studio premere il pulsante **Avvia**, ovvero il pulsante a forma di triangolo simile a un pulsante di riproduzione, per avviare l'applicazione all'interno del simulatore iOS remoto o dell'emulatore Android prescelto:

    [![Screenshot di visualizzazioni figlio orientate orizzontalmente in un controllo StackLayout, in iOS e Android](../images/orientation.png "StackLayout che contiene istanze di etichette orientate orizzontalmente")](../images/orientation-large.png#lightbox "StackLayout che contiene istanze di etichette orientate orizzontalmente")

    Si noti che le istanze di [`Label`](xref:Xamarin.Forms.Label) all'interno di [`StackLayout`](xref:Xamarin.Forms.StackLayout) sono ora allineate orizzontalmente anziché verticalmente.

# <a name="visual-studio-for-mac"></a>[Visual Studio per Mac](#tab/vsmac)

1. In **MainPage.xaml** modificare la dichiarazione [`StackLayout`](xref:Xamarin.Forms.StackLayout) per allineare gli elementi figlio orizzontalmente anziché verticalmente:

    ```xaml
    <StackLayout Margin="20,35,20,25"
                 Orientation="Horizontal">
        <Label Text="The StackLayout has its Margin property set, to control the rendering position of the StackLayout." />
        <Label Text="The Padding property can be set to specify the distance between the StackLayout and its children." />
        <Label Text="The Spacing property can be set to specify the distance between views in the StackLayout." />
    </StackLayout>
    ```

    Questo codice imposta la proprietà [`Orientation`](xref:Xamarin.Forms.StackLayout.Orientation) su [`Horizontal`](xref:Xamarin.Forms.StackOrientation.Horizontal).

1. Nella barra degli strumenti di Visual Studio per Mac premere il pulsante **Avvia**, ovvero il pulsante a forma di triangolo simile a un pulsante di riproduzione, per avviare l'applicazione all'interno del simulatore iOS o dell'emulatore Android prescelto:

    [![Screenshot di visualizzazioni figlio orientate orizzontalmente in un controllo StackLayout, in iOS e Android](../images/orientation.png "StackLayout che contiene istanze di etichette orientate orizzontalmente")](../images/orientation-large.png#lightbox "StackLayout che contiene istanze di etichette orientate orizzontalmente")

    Si noti che le istanze di [`Label`](xref:Xamarin.Forms.Label) all'interno di [`StackLayout`](xref:Xamarin.Forms.StackLayout) sono ora allineate orizzontalmente anziché verticalmente.
