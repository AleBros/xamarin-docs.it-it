---
ms.openlocfilehash: df2fa04deafc4438798593d3f25b85ec17363996
ms.sourcegitcommit: a153623a69b5cb125f672df8007838afa32e9edf
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/20/2019
ms.locfileid: "67277285"
---
# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Per completare questa esercitazione è necessario Visual Studio 2019 (la versione più recente) con installato il carico di lavoro **Sviluppo di applicazioni per dispositivi mobili con .NET**. È inoltre necessario un Mac associato per compilare l'applicazione dell'esercitazione per iOS. Per informazioni sull'installazione della piattaforma Xamarin, vedere [Installazione di Xamarin](~/get-started/installation/index.md). Per informazioni sulla connessione di Visual Studio 2019 a un host di compilazione Mac, vedere [Associa a Mac per lo sviluppo di Xamarin.iOS](~/ios/get-started/installation/windows/connecting-to-mac/index.md).

1. Avviare Visual Studio e creare una nuova app Xamarin.Forms vuota denominata **ImageTutorial**. Assicurarsi che l'app usi .NET Standard come meccanismo di codice condiviso.

    > [!IMPORTANT]
    > I frammenti di codice C# e XAML in questa esercitazione richiedono che la soluzione sia denominata **ImageTutorial**. Se si usa un nome diverso, si verificheranno errori di compilazione quando si copia il codice da questa esercitazione alla soluzione.

    Per altre informazioni sulla libreria .NET Standard che viene creata, vedere [Anatomia di un'applicazione Xamarin.Forms](~/get-started/first-app/index.md) in [Approfondimenti per le guide di avvio rapido di Xamarin.Forms](~/get-started/first-app/index.md).

1. Fare doppio clic su **MainPage.xaml** nel progetto **ImageTutorial** in **Esplora soluzioni** per aprire il file. In **MainPage.xaml** rimuovere quindi tutto il codice del modello e sostituirlo con il codice seguente:

    ```xaml
    <?xml version="1.0" encoding="utf-8"?>
    <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                 xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                 x:Class="ImageTutorial.MainPage">
        <StackLayout Margin="20,35,20,20">
            <Image Source="http://upload.wikimedia.org/wikipedia/commons/thumb/f/fc/Papio_anubis_%28Serengeti%2C_2009%29.jpg/200px-Papio_anubis_%28Serengeti%2C_2009%29.jpg"
                   HeightRequest="300" />
        </StackLayout>
    </ContentPage>
    ```

    Questo codice definisce in modo dichiarativo l'interfaccia utente per la pagina costituita da un elemento [`Image`](xref:Xamarin.Forms.Image) in uno [`StackLayout`](xref:Xamarin.Forms.StackLayout). La proprietà [`Image.Source`](xref:Xamarin.Forms.Image.Source) consente di specificare l'immagine da visualizzare, tramite un URI. La proprietà [`Image.Source`](xref:Xamarin.Forms.Image.Source) è di tipo [`ImageSource`](xref:Xamarin.Forms.ImageSource) e ciò consente di ottenere le immagini da file, URI o risorse. Per altre informazioni, vedere [Visualizzazione di immagini](~/xamarin-forms/user-interface/images.md#displaying-images) nella guida [Immagini in Xamarin.Forms](~/xamarin-forms/user-interface/images.md).

    La proprietà [`HeightRequest`](xref:Xamarin.Forms.VisualElement) specifica l'altezza dell'elemento `Image` in unità indipendenti dal dispositivo.

    > [!NOTE]
    > Non è necessario impostare la proprietà [`WidthRequest`](xref:Xamarin.Forms.VisualElement.WidthRequest) in questo esempio, perché l'elemento [`Image`](xref:Xamarin.Forms.Image) mantiene le proporzioni dell'immagine per impostazione predefinita.

1. Nella barra degli strumenti di Visual Studio premere il pulsante **Avvia**, ovvero il pulsante a forma di triangolo simile a un pulsante di riproduzione, per avviare l'applicazione all'interno del simulatore iOS remoto o dell'emulatore Android prescelto:

    [![Screenshot di un elemento Image in iOS e Android](../images/create-image.png "Visualizzazione di Image che mostra un'immagine")](../images/create-image-large.png#lightbox "Visualizzazione di Image che mostra un'immagine")

    > [!NOTE]
    > La visualizzazione di [`Image`](xref:Xamarin.Forms.Image) memorizza automaticamente nella cache le immagini scaricate per 24 ore. Per altre informazioni, vedere [Memorizzazione nella cache delle immagini scaricate](~/xamarin-forms/user-interface/images.md#downloaded-image-caching) nella guida [Immagini in Xamarin.Forms](~/xamarin-forms/user-interface/images.md).

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

Per completare questa esercitazione è necessario Visual Studio per Mac, la versione più recente, con installato il supporto della piattaforma Android e iOS. Sarà necessario anche Xcode, la versione più recente. Per altre informazioni sull'installazione della piattaforma Xamarin, vedere [Installazione di Xamarin](~/get-started/installation/index.md).

1. Avviare Visual Studio per Mac e creare una nuova app Xamarin.Forms vuota denominata **ImageTutorial**. Assicurarsi che l'app usi .NET Standard come meccanismo di codice condiviso.

    > [!IMPORTANT]
    > I frammenti di codice C# e XAML in questa esercitazione richiedono che la soluzione sia denominata **ImageTutorial**. Se si usa un nome diverso, si verificheranno errori di compilazione quando si copia il codice da questa esercitazione alla soluzione.

    Per altre informazioni sulla libreria .NET Standard che viene creata, vedere [Anatomia di un'applicazione Xamarin.Forms](~/get-started/first-app/index.md) in [Approfondimenti per le guide di avvio rapido di Xamarin.Forms](~/get-started/first-app/index.md).

1. Fare doppio clic su **MainPage.xaml** nel progetto **ImageTutorial** nel **riquadro della soluzione** per aprire il file. In **MainPage.xaml** rimuovere quindi tutto il codice del modello e sostituirlo con il codice seguente:

    ```xaml
    <?xml version="1.0" encoding="utf-8"?>
    <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                 xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                 x:Class="ImageTutorial.MainPage">
        <StackLayout Margin="20,35,20,20">
            <Image Source="http://upload.wikimedia.org/wikipedia/commons/thumb/f/fc/Papio_anubis_%28Serengeti%2C_2009%29.jpg/200px-Papio_anubis_%28Serengeti%2C_2009%29.jpg"
                   HeightRequest="300" />
        </StackLayout>
    </ContentPage>
    ```

    Questo codice definisce in modo dichiarativo l'interfaccia utente per la pagina costituita da un elemento [`Image`](xref:Xamarin.Forms.Image) in uno [`StackLayout`](xref:Xamarin.Forms.StackLayout). La proprietà [`Image.Source`](xref:Xamarin.Forms.Image.Source) consente di specificare l'immagine da visualizzare, tramite un URI. La proprietà [`Image.Source`](xref:Xamarin.Forms.Image.Source) è di tipo [`ImageSource`](xref:Xamarin.Forms.ImageSource) e ciò consente di ottenere le immagini da file, URI o risorse. Per altre informazioni, vedere [Visualizzazione di immagini](~/xamarin-forms/user-interface/images.md#displaying-images) nella guida [Immagini in Xamarin.Forms](~/xamarin-forms/user-interface/images.md).

    La proprietà [`HeightRequest`](xref:Xamarin.Forms.VisualElement) specifica l'altezza dell'elemento `Image` in unità indipendenti dal dispositivo.

    > [!NOTE]
    > Non è necessario impostare la proprietà [`WidthRequest`](xref:Xamarin.Forms.VisualElement.WidthRequest) in questo esempio, perché l'elemento [`Image`](xref:Xamarin.Forms.Image) mantiene le proporzioni dell'immagine per impostazione predefinita.

1. Nella barra degli strumenti di Visual Studio per Mac premere il pulsante **Avvia**, ovvero il pulsante a forma di triangolo simile a un pulsante di riproduzione, per avviare l'applicazione all'interno del simulatore iOS o dell'emulatore Android prescelto:

    [![Screenshot di un elemento Image in iOS e Android](../images/create-image.png "Visualizzazione di Image che mostra un'immagine")](../images/create-image-large.png#lightbox "Visualizzazione di Image che mostra un'immagine")

    > [!NOTE]
    > La visualizzazione di [`Image`](xref:Xamarin.Forms.Image) memorizza automaticamente nella cache le immagini scaricate per 24 ore. Per altre informazioni, vedere [Memorizzazione nella cache delle immagini scaricate](~/xamarin-forms/user-interface/images.md#downloaded-image-caching) nella guida [Immagini in Xamarin.Forms](~/xamarin-forms/user-interface/images.md).
