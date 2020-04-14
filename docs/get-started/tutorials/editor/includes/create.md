---
ms.openlocfilehash: fe7e11f37f303c2ec1f5bc58e98953449694191c
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/09/2020
ms.locfileid: "77135106"
---
# <a name="visual-studio"></a>[Visual Studio](#tab/vswin)

Per completare questa esercitazione è necessario Visual Studio 2019 (la versione più recente) con installato il carico di lavoro **Sviluppo di applicazioni per dispositivi mobili con .NET**. È inoltre necessario un Mac associato per compilare l'applicazione dell'esercitazione per iOS. Per informazioni sull'installazione della piattaforma Xamarin, vedere [Installazione di Xamarin](~/get-started/installation/index.md). Per informazioni sulla connessione di Visual Studio 2019 a un host di compilazione Mac, vedere [Associa a Mac per lo sviluppo di Xamarin.iOS](~/ios/get-started/installation/windows/connecting-to-mac/index.md).

1. Avviare Visual Studio e creare una nuova app Xamarin.Forms vuota denominata **EditorTutorial**. Assicurarsi che l'app usi .NET Standard come meccanismo di codice condiviso.

    > [!IMPORTANT]
    > I frammenti di codice C# e XAML di questa esercitazione richiedono che la soluzione sia denominata **EditorTutorial**. Se si usa un nome diverso, si verificheranno errori di compilazione quando si copia il codice da questa esercitazione alla soluzione.

    Per altre informazioni sulla libreria .NET Standard che viene creata, vedere [Anatomia di un'applicazione Xamarin.Forms](~/get-started/first-app/index.md) in [Approfondimenti per le guide di avvio rapido di Xamarin.Forms](~/get-started/first-app/index.md).

1. In **Esplora soluzioni**, nel progetto **EditorTutorial**, fare doppio clic su **MainPage.xaml** per aprire il file. In **MainPage.xaml** rimuovere quindi tutto il codice del modello e sostituirlo con il codice seguente:

    ```xaml
    <?xml version="1.0" encoding="utf-8"?>
    <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                 xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                 x:Class="EditorTutorial.MainPage">
        <StackLayout Margin="20,35,20,20">
            <Editor Placeholder="Enter multi-line text here"
                    HeightRequest="200" />
        </StackLayout>
    </ContentPage>
    ```

    Questo codice definisce in modo dichiarativo l'interfaccia utente per la pagina costituita da un elemento [`Editor`](xref:Xamarin.Forms.Editor) in uno [`StackLayout`](xref:Xamarin.Forms.StackLayout). La proprietà [`Editor.Placeholder`](xref:Xamarin.Forms.InputView.Placeholder) specifica il testo segnaposto mostrato alla prima visualizzazione di `Editor`. Inoltre, la proprietà [`HeightRequest`](xref:Xamarin.Forms.VisualElement) specifica l'altezza dell'elemento `Editor` in unità indipendenti dal dispositivo.

1. Nella barra degli strumenti di Visual Studio premere il pulsante **Avvia**, ovvero il pulsante a forma di triangolo simile a un pulsante di riproduzione, per avviare l'applicazione all'interno del simulatore iOS remoto o dell'emulatore Android prescelto:

    [![Screenshot di un editor in iOS e Android](../images/create-editor.png "Editor contenente testo segnaposto")](../images/create-editor-large.png#lightbox "Editor contenente testo segnaposto")

    > [!NOTE]
    > Mentre Android indica l'altezza dell'elemento [`Editor`](xref:Xamarin.Forms.Editor), iOS non lo fa.

# <a name="visual-studio-for-mac"></a>[Visual Studio per Mac](#tab/vsmac)

Per completare questa esercitazione è necessario Visual Studio per Mac, la versione più recente, con installato il supporto della piattaforma Android e iOS. Sarà necessario anche Xcode, la versione più recente. Per altre informazioni sull'installazione della piattaforma Xamarin, vedere [Installazione di Xamarin](~/get-started/installation/index.md).

1. Avviare Visual Studio per Mac e creare una nuova app Xamarin.Forms vuota denominata **EditorTutorial**. Assicurarsi che l'app usi .NET Standard come meccanismo di codice condiviso.

    > [!IMPORTANT]
    > I frammenti di codice C# e XAML di questa esercitazione richiedono che la soluzione sia denominata **EditorTutorial**. Se si usa un nome diverso, si verificheranno errori di compilazione quando si copia il codice da questa esercitazione alla soluzione.

    Per altre informazioni sulla libreria .NET Standard che viene creata, vedere [Anatomia di un'applicazione Xamarin.Forms](~/get-started/first-app/index.md) in [Approfondimenti per le guide di avvio rapido di Xamarin.Forms](~/get-started/first-app/index.md).

1. Nel **riquadro della soluzione**, nel progetto **EditorTutorial**, fare doppio clic su **MainPage.xaml** per aprire il file. In **MainPage.xaml** rimuovere quindi tutto il codice del modello e sostituirlo con il codice seguente:

    ```xaml
    <?xml version="1.0" encoding="utf-8"?>
    <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                 xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                 x:Class="EditorTutorial.MainPage">
        <StackLayout Margin="20,35,20,20">
            <Editor Placeholder="Enter multi-line text here"
                    HeightRequest="200" />
        </StackLayout>
    </ContentPage>
    ```

    Questo codice definisce in modo dichiarativo l'interfaccia utente per la pagina costituita da un elemento [`Editor`](xref:Xamarin.Forms.Editor) in uno [`StackLayout`](xref:Xamarin.Forms.StackLayout). La proprietà [`Editor.Placeholder`](xref:Xamarin.Forms.InputView.Placeholder) specifica il testo segnaposto mostrato alla prima visualizzazione di `Editor`. Inoltre, la proprietà [`HeightRequest`](xref:Xamarin.Forms.VisualElement) specifica l'altezza dell'elemento `Editor` in unità indipendenti dal dispositivo.

1. Nella barra degli strumenti di Visual Studio per Mac premere il pulsante **Avvia**, ovvero il pulsante a forma di triangolo simile a un pulsante di riproduzione, per avviare l'applicazione all'interno del simulatore iOS o dell'emulatore Android prescelto:

    [![Screenshot di un editor in iOS e Android](../images/create-editor.png "Editor contenente testo segnaposto")](../images/create-editor-large.png#lightbox "Editor contenente testo segnaposto")

    > [!NOTE]
    > Mentre Android indica l'altezza dell'elemento [`Editor`](xref:Xamarin.Forms.Editor), iOS non lo fa.
