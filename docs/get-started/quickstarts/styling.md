---
title: Definire lo stile di un'applicazione Xamarin.Forms multipiattaforma
description: Questo articolo illustra come applicare uno stile a un'applicazione Xamarin.Forms multipiattaforma con stili XAML.
zone_pivot_groups: platform
ms.topic: quickstart
ms.prod: xamarin
ms.assetid: CCCF8E57-D021-4542-8709-5808570FC26A
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/02/2019
ms.openlocfilehash: a7e1cdd59b463c38be1a49e962112cb893eed50f
ms.sourcegitcommit: d0e6436edbf7c52d760027d5e0ccaba2531d9fef
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75488868"
---
# <a name="style-a-cross-platform-xamarinforms-application"></a>Applicare uno stile a un'applicazione Xamarin.Forms multipiattaforma

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/getstarted-notes-styled/)

In questa Guida introduttiva si apprenderà come:

- Applicare uno stile a un'applicazione Xamarin.Forms usando gli stili XAML.

La Guida introduttiva illustra come applicare uno stile a un'applicazione Xamarin.Forms multipiattaforma con stili XAML. L'applicazione finale è riportata di seguito:

[![](styling-images/screenshots1-sml.png "Notes Page")](styling-images/screenshots1.png#lightbox "Notes Page")
[![](styling-images/screenshots2-sml.png "Note Entry Page")](styling-images/screenshots2.png#lightbox "Note Entry Page")

### <a name="prerequisites"></a>Prerequisiti

Prima di provare questa Guida introduttiva, è necessario completare correttamente la [Guida introduttiva precedente](database.md) . In alternativa, scaricare l' [esempio di Guida introduttiva precedente](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/getstarted-notes-database/) e usarlo come punto di partenza per questa Guida introduttiva.

::: zone pivot="windows"

## <a name="update-the-app-with-visual-studio"></a>Aggiornare l'app con Visual Studio

1. Avviare Visual Studio e aprire la soluzione note.

2. In **Esplora soluzioni**, nel progetto **note** , fare doppio clic su **app. XAML** per aprirlo. Sostituire quindi il codice esistente con il codice seguente:

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

    Questo codice definisce un valore [`Thickness`](xref:Xamarin.Forms.Thickness) , una serie di valori [`Color`](xref:Xamarin.Forms.Color) e stili impliciti per il [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) e la [`ContentPage`](xref:Xamarin.Forms.ContentPage). Si noti che questi stili, che sono nel [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary)a livello di applicazione, possono essere usati in tutta l'applicazione. Per ulteriori informazioni sullo stile XAML, vedere la pagina relativa all'applicazione di [stili](deepdive.md#styling) nella [Guida introduttiva a Xamarin.Forms](deepdive.md).

    Salvare le modifiche apportate a **app. XAML** premendo **CTRL + S**e chiudere il file.

3. In **Esplora soluzioni**, nel progetto **note** , fare doppio clic su **NotesPage. XAML** per aprirlo. Sostituire quindi il codice esistente con il codice seguente:

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

    Questo codice aggiunge uno stile implicito per la [`ListView`](xref:Xamarin.Forms.ListView) al [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary)a livello di pagina e imposta la proprietà `ListView.Margin` su un valore definito nel `ResourceDictionary`a livello di applicazione. Si noti che lo stile `ListView` implicito è stato aggiunto al `ResourceDictionary`a livello di pagina, perché viene utilizzato solo dal `NotesPage`. Per ulteriori informazioni sullo stile XAML, vedere la pagina relativa all'applicazione di [stili](deepdive.md#styling) nella [Guida introduttiva a Xamarin.Forms](deepdive.md).

    Salvare le modifiche apportate a **NotesPage. XAML** premendo **CTRL + S**e chiudere il file.

4. In **Esplora soluzioni**, nel progetto **note** , fare doppio clic su **NoteEntryPage. XAML** per aprirlo. Sostituire quindi il codice esistente con il codice seguente:

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
                <Setter Property="CornerRadius" Value="5" />
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

    Questo codice aggiunge stili impliciti per il [`Editor`](xref:Xamarin.Forms.Editor) e [`Button`](xref:Xamarin.Forms.Button) visualizzazioni al [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary)a livello di pagina e imposta la proprietà `StackLayout.Margin` su un valore definito nella `ResourceDictionary`a livello di applicazione. Si noti che gli stili impliciti `Editor` e `Button` sono stati aggiunti al `ResourceDictionary`a livello di pagina, perché vengono utilizzati solo dal `NoteEntryPage`. Per ulteriori informazioni sullo stile XAML, vedere la pagina relativa all'applicazione di [stili](deepdive.md#styling) nella [Guida introduttiva a Xamarin.Forms](deepdive.md).

    Salvare le modifiche apportate a **NoteEntryPage. XAML** premendo **CTRL + S**e chiudere il file.

5. Compilare ed eseguire il progetto in ogni piattaforma. Per ulteriori informazioni, vedere [la pagina relativa alla compilazione della Guida introduttiva](single-page.md#building-the-quickstart).

    In **NotesPage** premere il pulsante **+** per passare al **NoteEntryPage** e immettere una nota. In ogni pagina osservare come lo stile è stato modificato rispetto alla Guida introduttiva precedente.

::: zone-end
::: zone pivot="macos"

## <a name="update-the-app-with-visual-studio-for-mac"></a>Aggiornare l'app con Visual Studio per Mac

1. Avviare Visual Studio per Mac e aprire il progetto note.

2. Nel **riquadro della soluzione**, nel progetto **note** , fare doppio clic su **app. XAML** per aprirlo. Sostituire quindi il codice esistente con il codice seguente:

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

    Questo codice definisce un valore [`Thickness`](xref:Xamarin.Forms.Thickness) , una serie di valori [`Color`](xref:Xamarin.Forms.Color) e stili impliciti per il [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) e la [`ContentPage`](xref:Xamarin.Forms.ContentPage). Si noti che questi stili, che sono nel [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary)a livello di applicazione, possono essere usati in tutta l'applicazione. Per ulteriori informazioni sullo stile XAML, vedere la pagina relativa all'applicazione di [stili](deepdive.md#styling) nella [Guida introduttiva a Xamarin.Forms](deepdive.md).

    Salvare le modifiche apportate a **app. XAML** scegliendo **file > Salva** (o premendo  **&#8984; + S**) e chiudere il file.

3. Nel **riquadro della soluzione**, nel progetto **Notes** , fare doppio clic su **NotesPage. XAML** per aprirlo. Sostituire quindi il codice esistente con il codice seguente:

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

    Questo codice aggiunge uno stile implicito per la [`ListView`](xref:Xamarin.Forms.ListView) al [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary)a livello di pagina e imposta la proprietà `ListView.Margin` su un valore definito nel `ResourceDictionary`a livello di applicazione. Si noti che lo stile `ListView` implicito è stato aggiunto al `ResourceDictionary`a livello di pagina, perché viene utilizzato solo dal `NotesPage`. Per ulteriori informazioni sullo stile XAML, vedere la pagina relativa all'applicazione di [stili](deepdive.md#styling) nella [Guida introduttiva a Xamarin.Forms](deepdive.md).

    Salvare le modifiche apportate a **NotesPage. XAML** scegliendo **file > Salva** (o premendo  **&#8984; + S**) e chiudere il file.

4. Nel **riquadro della soluzione**, nel progetto **Notes** , fare doppio clic su **NoteEntryPage. XAML** per aprirlo. Sostituire quindi il codice esistente con il codice seguente:

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
                <Setter Property="CornerRadius" Value="5" />
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

    Questo codice aggiunge stili impliciti per il [`Editor`](xref:Xamarin.Forms.Editor) e [`Button`](xref:Xamarin.Forms.Button) visualizzazioni al [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary)a livello di pagina e imposta la proprietà `StackLayout.Margin` su un valore definito nella `ResourceDictionary`a livello di applicazione. Si noti che gli stili impliciti `Editor` e `Button` sono stati aggiunti al `ResourceDictionary`a livello di pagina, perché vengono utilizzati solo dal `NoteEntryPage`. Per ulteriori informazioni sullo stile XAML, vedere la pagina relativa all'applicazione di [stili](deepdive.md#styling) nella [Guida introduttiva a Xamarin.Forms](deepdive.md).

    Salvare le modifiche apportate a **NoteEntryPage. XAML** scegliendo **file > Salva** (o premendo  **&#8984; + S**) e chiudere il file.

5. Compilare ed eseguire il progetto in ogni piattaforma. Per ulteriori informazioni, vedere [la pagina relativa alla compilazione della Guida introduttiva](single-page.md#building-the-quickstart).

    In **NotesPage** premere il pulsante **+** per passare al **NoteEntryPage** e immettere una nota. In ogni pagina osservare come lo stile è stato modificato rispetto alla Guida introduttiva precedente.

::: zone-end

## <a name="next-steps"></a>Passaggi successivi

In questa guida introduttiva si è appreso come:

- Applicare uno stile a un'applicazione Xamarin.Forms usando gli stili XAML.

Per altre informazioni sulle nozioni di base dello sviluppo di applicazioni con Xamarin.Forms, passare alla Guida introduttiva di avvio rapido.

> [!div class="nextstepaction"]
> [Successivo](deepdive.md)

## <a name="related-links"></a>Collegamenti correlati

- [Notes (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/getstarted-notes-styled/)
- [Approfondimento sulla Guida introduttiva a Xamarin.Forms](deepdive.md)
