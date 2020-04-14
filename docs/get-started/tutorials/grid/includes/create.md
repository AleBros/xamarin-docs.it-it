---
ms.openlocfilehash: b1a041f1a2baae9b06de023f6eae9c6598b80061
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/09/2020
ms.locfileid: "72678723"
---
# <a name="visual-studio"></a>[Visual Studio](#tab/vswin)

Per completare questa esercitazione è necessario Visual Studio 2019 (la versione più recente) con installato il carico di lavoro **Sviluppo di applicazioni per dispositivi mobili con .NET**. È inoltre necessario un Mac associato per compilare l'applicazione dell'esercitazione per iOS. Per informazioni sull'installazione della piattaforma Xamarin, vedere [Installazione di Xamarin](~/get-started/installation/index.md). Per informazioni sulla connessione di Visual Studio 2019 a un host di compilazione Mac, vedere [Associa a Mac per lo sviluppo di Xamarin.iOS](~/ios/get-started/installation/windows/connecting-to-mac/index.md).

1. Avviare Visual Studio e creare una nuova app Xamarin.Forms vuota denominata **GridTutorial**. Assicurarsi che l'app usi .NET Standard come meccanismo di codice condiviso.

    > [!IMPORTANT]
    > I frammenti di codice C# e XAML in questa esercitazione richiedono che la soluzione sia denominata **GridTutorial**. Se si usa un nome diverso, si verificheranno errori di compilazione quando si copia il codice da questa esercitazione alla soluzione.

    Per altre informazioni sulla libreria .NET Standard che viene creata, vedere [Anatomia di un'applicazione Xamarin.Forms](~/get-started/first-app/index.md) in [Approfondimenti per le guide di avvio rapido di Xamarin.Forms](~/get-started/first-app/index.md).

1. Fare doppio clic su **MainPage.xaml** nel progetto **GridTutorial** in **Esplora soluzioni** per aprire il file. In **MainPage.xaml** rimuovere quindi tutto il codice del modello e sostituirlo con il codice seguente:

    ```xaml
    <?xml version="1.0" encoding="utf-8"?>
    <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                 xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                 x:Class="GridTutorial.MainPage">
        <Grid Margin="20,35,20,20">
            <Label Text="The Grid has its Margin property set, to control the rendering position of the Grid." />
        </Grid>
    </ContentPage>
    ```

    Questo codice definisce in modo dichiarativo l'interfaccia utente per la pagina costituita da un elemento [`Label`](xref:Xamarin.Forms.Label) in un elemento [`Grid`](xref:Xamarin.Forms.Grid). Per impostazione predefinita, `Grid` posiziona le relative visualizzazioni figlio in un'unica posizione. Pertanto, un `Grid` che contiene più elementi figlio deve specificare colonne e righe, che verranno trattate nel prossimo esercizio. Inoltre la proprietà [`Margin`](xref:Xamarin.Forms.View.Margin) indica la posizione di rendering di `Grid` all'interno di [`ContentPage`](xref:Xamarin.Forms.ContentPage).

    > [!NOTE]
    > Oltre alla proprietà [`Margin`](xref:Xamarin.Forms.View.Margin), anche la proprietà [`Padding`](xref:Xamarin.Forms.Layout.Padding) può essere impostata su un [`Grid`](xref:Xamarin.Forms.Grid). Il valore della proprietà [`Padding`](xref:Xamarin.Forms.Layout.Padding) specifica la distanza tra i limiti del controllo `Grid` e i relativi elementi figlio. Per altre informazioni, vedere l'articolo sulle proprietà [Margin e Padding](~/xamarin-forms/user-interface/layouts/margin-and-padding.md).

1. Nella barra degli strumenti di Visual Studio premere il pulsante **Avvia**, ovvero il pulsante a forma di triangolo simile a un pulsante di riproduzione, per avviare l'applicazione all'interno del simulatore remoto iOS o dell'emulatore Android prescelto:

    [![Screenshot di un'etichetta in un controllo Grid, in iOS e Android](../images/create-grid.png "Controllo Grid contenente un'etichetta")](../images/create-grid-large.png#lightbox "Controllo Grid contenente un'etichetta")

    Per altre informazioni su [`Grid`](xref:Xamarin.Forms.Grid), vedere [Griglia di Xamarin.Forms](~/xamarin-forms/user-interface/layouts/grid.md).

# <a name="visual-studio-for-mac"></a>[Visual Studio per Mac](#tab/vsmac)

Per completare questa esercitazione è necessario Visual Studio per Mac, la versione più recente, con installato il supporto della piattaforma Android e iOS. Sarà necessario anche Xcode, la versione più recente. Per altre informazioni sull'installazione della piattaforma Xamarin, vedere [Installazione di Xamarin](~/get-started/installation/index.md).

1. Avviare Visual Studio per Mac e creare una nuova app Xamarin.Forms vuota denominata **GridTutorial**. Assicurarsi che l'app usi .NET Standard come meccanismo di codice condiviso.

    > [!IMPORTANT]
    > I frammenti di codice C# e XAML in questa esercitazione richiedono che la soluzione sia denominata **GridTutorial**. Se si usa un nome diverso, si verificheranno errori di compilazione quando si copia il codice da questa esercitazione alla soluzione.

    Per altre informazioni sulla libreria .NET Standard che viene creata, vedere [Anatomia di un'applicazione Xamarin.Forms](~/get-started/first-app/index.md) in [Approfondimenti per le guide di avvio rapido di Xamarin.Forms](~/get-started/first-app/index.md).

1. Fare doppio clic su **MainPage.xaml** nel progetto **GridTutorial** nel **riquadro della soluzione** per aprire il file. In **MainPage.xaml** rimuovere quindi tutto il codice del modello e sostituirlo con il codice seguente:

    ```xaml
    <?xml version="1.0" encoding="utf-8"?>
    <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                 xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                 x:Class="GridTutorial.MainPage">
        <Grid Margin="20,35,20,20">
            <Label Text="The Grid has its Margin property set, to control the rendering position of the Grid." />
        </Grid>
    </ContentPage>
    ```

    Questo codice definisce in modo dichiarativo l'interfaccia utente per la pagina costituita da un elemento [`Label`](xref:Xamarin.Forms.Label) in un elemento [`Grid`](xref:Xamarin.Forms.Grid). Per impostazione predefinita, `Grid` posiziona le relative visualizzazioni figlio in un'unica posizione. Pertanto, un `Grid` che contiene più elementi figlio deve specificare colonne e righe, che verranno trattate nel prossimo esercizio. Inoltre la proprietà [`Margin`](xref:Xamarin.Forms.View.Margin) indica la posizione di rendering di `Grid` all'interno di [`ContentPage`](xref:Xamarin.Forms.ContentPage).

    > [!NOTE]
    > Oltre alla proprietà [`Margin`](xref:Xamarin.Forms.View.Margin), anche la proprietà [`Padding`](xref:Xamarin.Forms.Layout.Padding) può essere impostata su un [`Grid`](xref:Xamarin.Forms.Grid). Il valore della proprietà [`Padding`](xref:Xamarin.Forms.Layout.Padding) specifica la distanza tra le visualizzazioni in `Grid`. Per altre informazioni, vedere l'articolo sulle proprietà [Margin e Padding](~/xamarin-forms/user-interface/layouts/margin-and-padding.md).

1. Nella barra degli strumenti di Visual Studio per Mac premere il pulsante **Avvia**, ovvero il pulsante a forma di triangolo simile a un pulsante di riproduzione, per avviare l'applicazione all'interno del simulatore iOS o dell'emulatore Android prescelto:

    [![Screenshot di un'etichetta in un controllo Grid, in iOS e Android](../images/create-grid.png "Controllo Grid contenente un'etichetta")](../images/create-grid-large.png#lightbox "Controllo Grid contenente un'etichetta")

    Per altre informazioni su [`Grid`](xref:Xamarin.Forms.Grid), vedere [Griglia di Xamarin.Forms](~/xamarin-forms/user-interface/layouts/grid.md).
