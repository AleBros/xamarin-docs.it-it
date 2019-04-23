---
ms.openlocfilehash: ce3e0f18d299d2bdf8d9bd81c467d45924d0d2bc
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61373389"
---
# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. Avviare Visual Studio e creare una nuova app Xamarin.Forms vuota denominata **EditorTutorial**. Assicurarsi che l'app usi .NET Standard come meccanismo di codice condiviso.

    > [!IMPORTANT]
    > I frammenti di codice C# e XAML di questa esercitazione richiedono che la soluzione sia denominata **EditorTutorial**. Se si usa un nome diverso, si verificheranno errori di compilazione quando si copia il codice da questa esercitazione alla soluzione.

    Per altre informazioni sulla libreria .NET Standard che viene creata, vedere [Anatomy of a Xamarin.Forms application](~/get-started/first-app/index.md) (Anatomia di un'applicazione Xamarin.Forms) in [Xamarin.Forms Quickstart Deep Dive](~/get-started/first-app/index.md) (Approfondimenti per l'avvio rapido di Xamarin.Forms).

1. In **Esplora soluzioni**, nel progetto **EditorTutorial**, fare doppio clic su **MainPage.xaml** per aprire il file. In **MainPage.xaml** rimuovere tutto il codice del modello e sostituirlo con il codice seguente:

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

    Questo codice definisce in modo dichiarativo l'interfaccia utente per la pagina costituita da un elemento [`Editor`](xref:Xamarin.Forms.Editor) in un elemento [`StackLayout`](xref:Xamarin.Forms.StackLayout). La proprietà [`Editor.Placeholder`](xref:Xamarin.Forms.Editor.Placeholder) specifica il testo segnaposto mostrato alla prima visualizzazione dell'elemento `Editor`. Inoltre, la proprietà [`HeightRequest`](xref:Xamarin.Forms.VisualElement) specifica l'altezza dell'elemento `Editor` in unità indipendenti dal dispositivo.

1. Nella barra degli strumenti di Visual Studio premere il pulsante **Avvia**, ovvero il pulsante a forma di triangolo simile a un pulsante di riproduzione, per avviare l'applicazione all'interno del simulatore iOS remoto o dell'emulatore Android prescelto:

    [![Screenshot di un elemento Editor in iOS e Android](../images/create-editor.png "Elemento Editor contenente testo segnaposto")](../images/create-editor-large.png#lightbox "Elemento Editor contenente testo segnaposto")

    > [!NOTE]
    > Mentre Android indica l'altezza dell'elemento [`Editor`](xref:Xamarin.Forms.Editor), iOS non lo fa.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

1. Avviare Visual Studio per Mac e creare una nuova app Xamarin.Forms vuota denominata **EditorTutorial**. Assicurarsi che l'app usi .NET Standard come meccanismo di codice condiviso.

    > [!IMPORTANT]
    > I frammenti di codice C# e XAML di questa esercitazione richiedono che la soluzione sia denominata **EditorTutorial**. Se si usa un nome diverso, si verificheranno errori di compilazione quando si copia il codice da questa esercitazione alla soluzione.

    Per altre informazioni sulla libreria .NET Standard che viene creata, vedere [Anatomy of a Xamarin.Forms application](~/get-started/first-app/index.md) (Anatomia di un'applicazione Xamarin.Forms) in [Xamarin.Forms Quickstart Deep Dive](~/get-started/first-app/index.md) (Approfondimenti per l'avvio rapido di Xamarin.Forms).

1. Nel **riquadro della soluzione**, nel progetto **EditorTutorial**, fare doppio clic su **MainPage.xaml** per aprire il file. In **MainPage.xaml** rimuovere tutto il codice del modello e sostituirlo con il codice seguente:

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

    Questo codice definisce in modo dichiarativo l'interfaccia utente per la pagina costituita da un elemento [`Editor`](xref:Xamarin.Forms.Editor) in un elemento [`StackLayout`](xref:Xamarin.Forms.StackLayout). La proprietà [`Editor.Placeholder`](xref:Xamarin.Forms.Editor.Placeholder) specifica il testo segnaposto mostrato alla prima visualizzazione dell'elemento `Editor`. Inoltre, la proprietà [`HeightRequest`](xref:Xamarin.Forms.VisualElement) specifica l'altezza dell'elemento `Editor` in unità indipendenti dal dispositivo.

1. Nella barra degli strumenti di Visual Studio per Mac premere il pulsante **Avvia**, ovvero il pulsante a forma di triangolo simile a un pulsante di riproduzione, per avviare l'applicazione all'interno del simulatore iOS o dell'emulatore Android prescelto:

    [![Screenshot di un elemento Editor in iOS e Android](../images/create-editor.png "Elemento Editor contenente testo segnaposto")](../images/create-editor-large.png#lightbox "Elemento Editor contenente testo segnaposto")

    > [!NOTE]
    > Mentre Android indica l'altezza dell'elemento [`Editor`](xref:Xamarin.Forms.Editor), iOS non lo fa.
