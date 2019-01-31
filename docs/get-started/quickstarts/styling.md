---
title: Stile di un'applicazione xamarin. Forms multipiattaforma
description: Questo articolo illustra come applicare uno stile a un'applicazione xamarin. Forms multipiattaforma con stili di visualizzazione XAML.
zone_pivot_groups: platform
ms.topic: quickstart
ms.prod: xamarin
ms.assetid: CCCF8E57-D021-4542-8709-5808570FC26A
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/02/2019
ms.openlocfilehash: 56f8632c9cc7d170b4c6594fd51e6aa3e92ad02b
ms.sourcegitcommit: 817d26585093cd180a36b28179eb354b0eb900b3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/30/2019
ms.locfileid: "55293038"
---
# <a name="style-a-cross-platform-xamarinforms-application"></a>Applicare uno stile a un'applicazione xamarin. Forms multipiattaforma

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://developer.xamarin.com/samples/xamarin-forms/GetStarted/Notes/Styled/)

In questa Guida introduttiva si apprenderà come:

- Applicare uno stile a un'applicazione xamarin. Forms usando gli stili XAML.

La Guida introduttiva illustra come applicare uno stile a un'applicazione xamarin. Forms multipiattaforma con stili di visualizzazione XAML. Il risultato è riportato di seguito:

[![](styling-images/screenshots1-sml.png "Note sulla pagina")](styling-images/screenshots1.png#lightbox "pagina note")
[![](styling-images/screenshots2-sml.png "notare voce pagina")](styling-images/screenshots2.png#lightbox "nota Pagina iniziale")

### <a name="prerequisites"></a>Prerequisiti

Si consiglia di completare correttamente il [Guida introduttiva precedente](database.md) prima di tentare di questa Guida introduttiva. In alternativa, scaricare il [esempio di Guida introduttiva precedente](https://developer.xamarin.com/samples/xamarin-forms/GetStarted/Notes/Database/) e usarlo come punto di partenza per questa Guida introduttiva.

::: zone pivot="windows"

## <a name="update-the-app-with-visual-studio"></a>Aggiornare l'app con Visual Studio

1. Avviare Visual Studio e aprire la soluzione note.

2. In **Esplora soluzioni**, nella **note** del progetto, fare doppio clic su **app** per aprirlo. Sostituire quindi il codice esistente con il codice seguente:

    ```xaml
    <?xml version="1.0" encoding="utf-8"?>
    <Application xmlns="http://xamarin.com/schemas/2014/forms"
                 xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                 x:Class="Notes.App">
        <Application.Resources>

            <Thickness x:Key="PageMargin">20</Thickness>

            <!-- Colors -->
            <Color x:Key="AppBackgroundColor">WhiteSmoke</Color>
            <Color x:Key="iOSNavigationBarColor">WhiteSmoke</Color>
            <Color x:Key="AndroidNavigationBarColor">#2196F3</Color>
            <Color x:Key="iOSNavigationBarTextColor">Black</Color>
            <Color x:Key="AndroidNavigationBarTextColor">White</Color>

            <!-- Implicit styles -->
            <Style TargetType="{x:Type NavigationPage}">
                <Setter Property="BarBackgroundColor"
                        Value="{OnPlatform iOS={StaticResource iOSNavigationBarColor},
                                           Android={StaticResource AndroidNavigationBarColor}}" />
                 <Setter Property="BarTextColor"
                        Value="{OnPlatform iOS={StaticResource iOSNavigationBarTextColor},
                                           Android={StaticResource AndroidNavigationBarTextColor}}" />           
            </Style>

            <Style TargetType="{x:Type ContentPage}"
                   ApplyToDerivedTypes="True">
                <Setter Property="BackgroundColor"
                        Value="{StaticResource AppBackgroundColor}" />
            </Style>

        </Application.Resources>
    </Application>
    ```

    Questo codice definisce una [ `Thickness` ](xref:Xamarin.Forms.Thickness) valore, una serie di [ `Color` ](xref:Xamarin.Forms.Color) valori e gli stili impliciti per il [ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage) e [ `ContentPage` ](xref:Xamarin.Forms.ContentPage). Si noti che questi stili, che si trovano in a livello di applicazione [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary), possono essere utilizzati in tutta l'applicazione. Per altre informazioni sull'applicazione di stili XAML, vedere [applicazione di stili](deepdive.md#styling) nel [approfondimento di avvio rapido di xamarin. Forms](deepdive.md).

    Salvare le modifiche apportate a **app. XAML** premendo **CTRL + S**e chiudere il file.

3. In **Esplora soluzioni**, nella **note** del progetto, fare doppio clic su **NotesPage.xaml** per aprirlo. Sostituire quindi il codice esistente con il codice seguente:

    ```xaml
    <?xml version="1.0" encoding="UTF-8"?>
    <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                 xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                 x:Class="Notes.NotesPage"
                 Title="Notes">
        <ContentPage.Resources>
            <!-- Implicit styles -->
            <Style TargetType="{x:Type ListView}">
                <Setter Property="BackgroundColor"
                        Value="{StaticResource AppBackgroundColor}" />
            </Style>
        </ContentPage.Resources>

        <ContentPage.ToolbarItems>
            <ToolbarItem Text="+"
                         Clicked="OnNoteAddedClicked" />
        </ContentPage.ToolbarItems>

        <ListView x:Name="listView"
                  Margin="{StaticResource PageMargin}"
                  ItemSelected="OnListViewItemSelected">
            <ListView.ItemTemplate>
                <DataTemplate>
                    <TextCell Text="{Binding Text}"
                              Detail="{Binding Date}" />
                </DataTemplate>
            </ListView.ItemTemplate>
        </ListView>

    </ContentPage>
    ```

    Questo codice aggiunge uno stile implicito per il [ `ListView` ](xref:Xamarin.Forms.ListView) a livello di pagina [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary)e imposta il `ListView.Margin` proprietà su un valore definito nel livello di applicazione `ResourceDictionary` . Si noti che il `ListView` stile implicito è stato aggiunto al livello della pagina `ResourceDictionary`, in quanto utilizzata solo per il `NotesPage`. Per altre informazioni sull'applicazione di stili XAML, vedere [applicazione di stili](deepdive.md#styling) nel [approfondimento di avvio rapido di xamarin. Forms](deepdive.md).

    Salvare le modifiche apportate a **NotesPage.xaml** premendo **CTRL + S**e chiudere il file.

5. Nel **riquadro della soluzione**, nella **note** del progetto, fare doppio clic su **NoteEntryPage.xaml** per aprirlo. Sostituire quindi il codice esistente con il codice seguente:

    ```xaml
    <?xml version="1.0" encoding="UTF-8"?>
    <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                 xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                 x:Class="Notes.NoteEntryPage"
                 Title="Note Entry">
        <ContentPage.Resources>
            <!-- Implicit styles -->
            <Style TargetType="{x:Type Editor}">
                <Setter Property="BackgroundColor"
                        Value="{StaticResource AppBackgroundColor}" />
            </Style>

            <Style TargetType="Button"
                   ApplyToDerivedTypes="True"
                   CanCascade="True">
                <Setter Property="FontSize" Value="Medium" />
                <Setter Property="BackgroundColor" Value="LightGray" />
                <Setter Property="TextColor" Value="Black" />
                <Setter Property="BorderRadius" Value="5" />
            </Style>
        </ContentPage.Resources>

        <StackLayout Margin="{StaticResource PageMargin}">
            <Editor Placeholder="Enter your note"
                    Text="{Binding Text}"
                    HeightRequest="100" />
            <Grid>
                <Grid.ColumnDefinitions>
                    <ColumnDefinition Width="*" />
                    <ColumnDefinition Width="*" />
                </Grid.ColumnDefinitions>
                <Button Text="Save"
                        Clicked="OnSaveButtonClicked" />
                <Button Grid.Column="1"
                        Text="Delete"
                        Clicked="OnDeleteButtonClicked" />
            </Grid>
        </StackLayout>

    </ContentPage>
    ```

    Questo codice aggiunge gli stili impliciti per il [ `Editor` ](xref:Xamarin.Forms.Editor) e [ `Button` ](xref:Xamarin.Forms.Button) viste a livello di pagina [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary)e imposta il `StackLayout.Margin` proprietà con un valore definito nel livello di applicazione `ResourceDictionary`. Si noti che il `Editor` e `Button` stili impliciti sono stati aggiunti a livello di pagina `ResourceDictionary`, in quanto vengono utilizzati solo dal `NoteEntryPage`. Per altre informazioni sull'applicazione di stili XAML, vedere [applicazione di stili](deepdive.md#styling) nel [approfondimento di avvio rapido di xamarin. Forms](deepdive.md).

    Salvare le modifiche apportate a **NoteEntryPage.xaml** premendo **CTRL + S**e chiudere il file.

6. Compilare ed eseguire il progetto in ciascuna piattaforma. Per altre informazioni, vedere [compilazione la Guida introduttiva](single-page.md#building-the-quickstart).

    Nel **NotesPage** premere il **+** pulsante per passare alla **NoteEntryPage** e immettere una nota. In ogni pagina di osservare come lo stile è stata modificata dalla Guida introduttiva precedente.

::: zone-end
::: zone pivot="macos"

## <a name="update-the-app-with-visual-studio-for-mac"></a>Aggiornare l'app con Visual Studio per Mac

1. Avviare Visual Studio per Mac e aprire il progetto note.

2. Nel **riquadro della soluzione**, nella **note** del progetto, fare doppio clic su **app** per aprirlo. Sostituire quindi il codice esistente con il codice seguente:

    ```xaml
    <?xml version="1.0" encoding="utf-8"?>
    <Application xmlns="http://xamarin.com/schemas/2014/forms"
                 xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                 x:Class="Notes.App">
        <Application.Resources>

            <Thickness x:Key="PageMargin">20</Thickness>

            <!-- Colors -->
            <Color x:Key="AppBackgroundColor">WhiteSmoke</Color>
            <Color x:Key="iOSNavigationBarColor">WhiteSmoke</Color>
            <Color x:Key="AndroidNavigationBarColor">#2196F3</Color>
            <Color x:Key="iOSNavigationBarTextColor">Black</Color>
            <Color x:Key="AndroidNavigationBarTextColor">White</Color>

            <!-- Implicit styles -->
            <Style TargetType="{x:Type NavigationPage}">
                <Setter Property="BarBackgroundColor"
                        Value="{OnPlatform iOS={StaticResource iOSNavigationBarColor},
                                           Android={StaticResource AndroidNavigationBarColor}}" />
                 <Setter Property="BarTextColor"
                        Value="{OnPlatform iOS={StaticResource iOSNavigationBarTextColor},
                                           Android={StaticResource AndroidNavigationBarTextColor}}" />           
            </Style>

            <Style TargetType="{x:Type ContentPage}"
                   ApplyToDerivedTypes="True">
                <Setter Property="BackgroundColor"
                        Value="{StaticResource AppBackgroundColor}" />
            </Style>

        </Application.Resources>
    </Application>
    ```

    Questo codice definisce una [ `Thickness` ](xref:Xamarin.Forms.Thickness) valore, una serie di [ `Color` ](xref:Xamarin.Forms.Color) valori e gli stili impliciti per il [ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage) e [ `ContentPage` ](xref:Xamarin.Forms.ContentPage). Si noti che questi stili, che si trovano in a livello di applicazione [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary), possono essere utilizzati in tutta l'applicazione. Per altre informazioni sull'applicazione di stili XAML, vedere [applicazione di stili](deepdive.md#styling) nel [approfondimento di avvio rapido di xamarin. Forms](deepdive.md).

    Salvare le modifiche apportate a **app. XAML** scegliendo **File > Salva** (o premendo  **&#8984; + S**) e chiudere il file.

3. In **Esplora soluzioni**, nella **note** del progetto, fare doppio clic su **NotesPage.xaml** per aprirlo. Sostituire quindi il codice esistente con il codice seguente:

    ```xaml
    <?xml version="1.0" encoding="UTF-8"?>
    <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                 xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                 x:Class="Notes.NotesPage"
                 Title="Notes">
        <ContentPage.Resources>
            <!-- Implicit styles -->
            <Style TargetType="{x:Type ListView}">
                <Setter Property="BackgroundColor"
                        Value="{StaticResource AppBackgroundColor}" />
            </Style>
        </ContentPage.Resources>

        <ContentPage.ToolbarItems>
            <ToolbarItem Text="+"
                         Clicked="OnNoteAddedClicked" />
        </ContentPage.ToolbarItems>

        <ListView x:Name="listView"
                  Margin="{StaticResource PageMargin}"
                  ItemSelected="OnListViewItemSelected">
            <ListView.ItemTemplate>
                <DataTemplate>
                    <TextCell Text="{Binding Text}"
                              Detail="{Binding Date}" />
                </DataTemplate>
            </ListView.ItemTemplate>
        </ListView>

    </ContentPage>
    ```

    Questo codice aggiunge uno stile implicito per il [ `ListView` ](xref:Xamarin.Forms.ListView) a livello di pagina [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary)e imposta il `ListView.Margin` proprietà su un valore definito nel livello di applicazione `ResourceDictionary` . Si noti che il `ListView` stile implicito è stato aggiunto al livello della pagina `ResourceDictionary`, in quanto utilizzata solo per il `NotesPage`. Per altre informazioni sull'applicazione di stili XAML, vedere [applicazione di stili](deepdive.md#styling) nel [approfondimento di avvio rapido di xamarin. Forms](deepdive.md).

    Salvare le modifiche apportate a **NotesPage.xaml** scegliendo **File > Salva** (o premendo  **&#8984; + S**) e chiudere il file.

5. In **Esplora soluzioni**, nella **note** del progetto, fare doppio clic su **NoteEntryPage.xaml** per aprirlo. Sostituire quindi il codice esistente con il codice seguente:

    ```xaml
    <?xml version="1.0" encoding="UTF-8"?>
    <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                 xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                 x:Class="Notes.NoteEntryPage"
                 Title="Note Entry">
        <ContentPage.Resources>
            <!-- Implicit styles -->
            <Style TargetType="{x:Type Editor}">
                <Setter Property="BackgroundColor"
                        Value="{StaticResource AppBackgroundColor}" />
            </Style>

            <Style TargetType="Button"
                   ApplyToDerivedTypes="True"
                   CanCascade="True">
                <Setter Property="FontSize" Value="Medium" />
                <Setter Property="BackgroundColor" Value="LightGray" />
                <Setter Property="TextColor" Value="Black" />
                <Setter Property="BorderRadius" Value="5" />
            </Style>
        </ContentPage.Resources>

        <StackLayout Margin="{StaticResource PageMargin}">
            <Editor Placeholder="Enter your note"
                    Text="{Binding Text}"
                    HeightRequest="100" />
            <Grid>
                <Grid.ColumnDefinitions>
                    <ColumnDefinition Width="*" />
                    <ColumnDefinition Width="*" />
                </Grid.ColumnDefinitions>
                <Button Text="Save"
                        Clicked="OnSaveButtonClicked" />
                <Button Grid.Column="1"
                        Text="Delete"
                        Clicked="OnDeleteButtonClicked" />
            </Grid>
        </StackLayout>

    </ContentPage>
    ```

    Questo codice aggiunge gli stili impliciti per il [ `Editor` ](xref:Xamarin.Forms.Editor) e [ `Button` ](xref:Xamarin.Forms.Button) viste a livello di pagina [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary)e imposta il `StackLayout.Margin` proprietà con un valore definito nel livello di applicazione `ResourceDictionary`. Si noti che il `Editor` e `Button` stili impliciti sono stati aggiunti a livello di pagina `ResourceDictionary`, in quanto vengono utilizzati solo dal `NoteEntryPage`. Per altre informazioni sull'applicazione di stili XAML, vedere [applicazione di stili](deepdive.md#styling) nel [approfondimento di avvio rapido di xamarin. Forms](deepdive.md).

    Salvare le modifiche apportate a **NoteEntryPage.xaml** scegliendo **File > Salva** (o premendo  **&#8984; + S**) e chiudere il file.

6. Compilare ed eseguire il progetto in ciascuna piattaforma. Per altre informazioni, vedere [compilazione la Guida introduttiva](single-page.md#building-the-quickstart).

    Nel **NotesPage** premere il **+** pulsante per passare alla **NoteEntryPage** e immettere una nota. In ogni pagina di osservare come lo stile è stata modificata dalla Guida introduttiva precedente.

::: zone-end


## <a name="next-steps"></a>Passaggi successivi

In questa Guida introduttiva si è appreso come:

- Applicare uno stile a un'applicazione xamarin. Forms usando gli stili XAML.

Per altre informazioni sui concetti fondamentali dello sviluppo di applicazioni mediante xamarin. Forms, continuare a approfondimenti di Guida introduttiva.

> [!div class="nextstepaction"]
> [avanti](deepdive.md)

## <a name="related-links"></a>Collegamenti correlati

- [Notes (esempio)](https://developer.xamarin.com/samples/xamarin-forms/GetStarted/Notes/Styled/)
- [Approfondimento di Guida introduttiva di xamarin. Forms](deepdive.md)
