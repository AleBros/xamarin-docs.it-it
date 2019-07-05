---
ms.openlocfilehash: 4ea10a3cc4eca0708d1de1aee68bed74c960cbc2
ms.sourcegitcommit: 8ecfa339d0f3e7687977bfe4fc96448942690183
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/03/2019
ms.locfileid: "67560001"
---
# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Per completare questa esercitazione è necessario Visual Studio 2019 (la versione più recente) con installato il carico di lavoro **Sviluppo di applicazioni per dispositivi mobili con .NET**. È inoltre necessario un Mac associato per compilare l'applicazione dell'esercitazione per iOS. Per informazioni sull'installazione della piattaforma Xamarin, vedere [Installazione di Xamarin](~/get-started/installation/index.md). Per informazioni sulla connessione di Visual Studio 2019 a un host di compilazione Mac, vedere [Associa a Mac per lo sviluppo di Xamarin.iOS](~/ios/get-started/installation/windows/connecting-to-mac/index.md).

1. Avviare Visual Studio e creare una nuova app Xamarin.Forms vuota denominata **StackLayoutTutorial**. Assicurarsi che l'app usi .NET Standard come meccanismo di codice condiviso.

    > [!IMPORTANT]
    > I frammenti di codice C# e XAML di questa esercitazione richiedono che la soluzione sia denominata **StackLayoutTutorial**. Se si usa un nome diverso, si verificheranno errori di compilazione quando si copia il codice da questa esercitazione alla soluzione.

    Per altre informazioni sulla libreria .NET Standard che viene creata, vedere [Anatomy of a Xamarin.Forms application](~/get-started/first-app/index.md) (Anatomia di un'applicazione Xamarin.Forms) in [Xamarin.Forms Quickstart Deep Dive](~/get-started/first-app/index.md) (Approfondimenti per l'avvio rapido di Xamarin.Forms).

1. In **Esplora soluzioni**, nel progetto **StackLayoutTutorial**, fare doppio clic su **MainPage.xaml** per aprire il file. In **MainPage.xaml** rimuovere tutto il codice del modello e sostituirlo con il codice seguente:

    ```xaml
    <?xml version="1.0" encoding="utf-8"?>
    <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                 xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                 x:Class="StackLayoutTutorial.MainPage">
        <StackLayout Margin="20,35,20,25">
            <Label Text="The StackLayout has its Margin property set, to control the rendering position of the StackLayout." />
            <Label Text="The Padding property can be set to specify the distance between the StackLayout and its children." />
            <Label Text="The Spacing property can be set to specify the distance between views in the StackLayout." />
        </StackLayout>
    </ContentPage>
    ```

    Questo codice definisce in modo dichiarativo l'interfaccia utente per la pagina costituita da tre istanze [`Label`](xref:Xamarin.Forms.Label) in un elemento [`StackLayout`](xref:Xamarin.Forms.StackLayout). L'elemento [`StackLayout`](xref:Xamarin.Forms.StackLayout) posiziona le relative viste figlio, ovvero le istanze `Label`, in una singola riga, che è orientata in verticale per impostazione predefinita. Inoltre la proprietà [`Margin`](xref:Xamarin.Forms.View.Margin) indica la posizione di rendering di `StackLayout` all'interno di [`ContentPage`](xref:Xamarin.Forms.ContentPage).

    > [!NOTE]
    > Oltre alla proprietà [`Margin`](xref:Xamarin.Forms.View.Margin), anche le proprietà [`Padding`](xref:Xamarin.Forms.Layout.Padding) e [`Spacing`](xref:Xamarin.Forms.StackLayout.Spacing) possono essere impostate su un [`StackLayout`](xref:Xamarin.Forms.StackLayout). Il valore della proprietà [`Padding`](xref:Xamarin.Forms.Layout.Padding) specifica la distanza tra le viste nell'elemento `StackLayout` e il valore della proprietà [`Spacing`](xref:Xamarin.Forms.StackLayout.Spacing) specifica la quantità di spazio tra i singoli elementi figlio in `StackLayout`. Per altre informazioni, vedere l'articolo sulle proprietà [Margin e Padding](~/xamarin-forms/user-interface/layouts/margin-and-padding.md).

1. Nella barra degli strumenti di Visual Studio premere il pulsante **Avvia**, ovvero il pulsante a forma di triangolo simile a un pulsante di riproduzione, per avviare l'applicazione all'interno del simulatore iOS remoto o dell'emulatore Android prescelto:

    [![Screenshot delle viste figlio in un elemento StackLayout in iOS e Android](../images/create-stacklayout.png "Elemento StackLayout contenente istanze di Label")](../images/create-stacklayout-large.png#lightbox "Elemento StackLayout contenente istanze di Label")

    Per altre informazioni su [`StackLayout`](xref:Xamarin.Forms.StackLayout), vedere [Xamarin.Forms StackLayout](~/xamarin-forms/user-interface/layouts/stack-layout.md) (StackLayout di Xamarin.Forms).

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

Per completare questa esercitazione è necessario Visual Studio per Mac, la versione più recente, con installato il supporto della piattaforma Android e iOS. Sarà necessario anche Xcode, la versione più recente. Per altre informazioni sull'installazione della piattaforma Xamarin, vedere [Installazione di Xamarin](~/get-started/installation/index.md).

1. Avviare Visual Studio per Mac e creare una nuova app Xamarin.Forms vuota denominata **StackLayoutTutorial**. Assicurarsi che l'app usi .NET Standard come meccanismo di codice condiviso.

    > [!IMPORTANT]
    > I frammenti di codice C# e XAML di questa esercitazione richiedono che la soluzione sia denominata **StackLayoutTutorial**. Se si usa un nome diverso, si verificheranno errori di compilazione quando si copia il codice da questa esercitazione alla soluzione.

    Per altre informazioni sulla libreria .NET Standard che viene creata, vedere [Anatomy of a Xamarin.Forms application](~/get-started/first-app/index.md) (Anatomia di un'applicazione Xamarin.Forms) in [Xamarin.Forms Quickstart Deep Dive](~/get-started/first-app/index.md) (Approfondimenti per l'avvio rapido di Xamarin.Forms).

1. Nel **riquadro della soluzione**, nel progetto **StackLayoutTutorial**, fare doppio clic su **MainPage.xaml** per aprire il file. In **MainPage.xaml** rimuovere tutto il codice del modello e sostituirlo con il codice seguente:

    ```xaml
    <?xml version="1.0" encoding="utf-8"?>
    <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                 xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                 x:Class="StackLayoutTutorial.MainPage">
        <StackLayout Margin="20,35,20,25">
            <Label Text="The StackLayout has its Margin property set, to control the rendering position of the StackLayout." />
            <Label Text="The Padding property can be set to specify the distance between the StackLayout and its children." />
            <Label Text="The Spacing property can be set to specify the distance between views in the StackLayout." />
        </StackLayout>
    </ContentPage>
    ```

    Questo codice definisce in modo dichiarativo l'interfaccia utente per la pagina costituita da tre istanze [`Label`](xref:Xamarin.Forms.Label) in un elemento [`StackLayout`](xref:Xamarin.Forms.StackLayout). L'elemento [`StackLayout`](xref:Xamarin.Forms.StackLayout) posiziona le relative viste figlio, ovvero le istanze `Label`, in una singola riga, che è orientata in verticale per impostazione predefinita. Inoltre la proprietà [`Margin`](xref:Xamarin.Forms.View.Margin) indica la posizione di rendering di `StackLayout` all'interno di [`ContentPage`](xref:Xamarin.Forms.ContentPage).

    > [!NOTE]
    > Oltre alla proprietà [`Margin`](xref:Xamarin.Forms.View.Margin), anche le proprietà [`Padding`](xref:Xamarin.Forms.Layout.Padding) e [`Spacing`](xref:Xamarin.Forms.StackLayout.Spacing) possono essere impostate su un [`StackLayout`](xref:Xamarin.Forms.StackLayout). Il valore della proprietà [`Padding`](xref:Xamarin.Forms.Layout.Padding) specifica la distanza tra le viste nell'elemento `StackLayout` e il valore della proprietà [`Spacing`](xref:Xamarin.Forms.StackLayout.Spacing) specifica la quantità di spazio tra i singoli elementi figlio in `StackLayout`. Per altre informazioni, vedere l'articolo sulle proprietà [Margin e Padding](~/xamarin-forms/user-interface/layouts/margin-and-padding.md).

1. Nella barra degli strumenti di Visual Studio per Mac premere il pulsante **Avvia**, ovvero il pulsante a forma di triangolo simile a un pulsante di riproduzione, per avviare l'applicazione all'interno del simulatore iOS o dell'emulatore Android prescelto:

    [![Screenshot delle viste figlio in un elemento StackLayout in iOS e Android](../images/create-stacklayout.png "Elemento StackLayout contenente istanze di Label")](../images/create-stacklayout-large.png#lightbox "Elemento StackLayout contenente istanze di Label")

    Per altre informazioni su [`StackLayout`](xref:Xamarin.Forms.StackLayout), vedere [Xamarin.Forms StackLayout](~/xamarin-forms/user-interface/layouts/stack-layout.md) (StackLayout di Xamarin.Forms).

-----

> [!div class="nextstepaction"]
> [Si è verificato un problema](https://github.com/MicrosoftDocs/xamarin-docs/issues/new?title=StackLayout+Tutorial+Step+1+Feedback&template=tutorial_template.md)
