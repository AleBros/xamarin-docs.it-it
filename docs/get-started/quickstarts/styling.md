---
title: Definire lo stile di un'applicazione Xamarin.Forms multipiattaforma
description: Questo articolo illustra come definire lo stile di un'applicazione Xamarin.Forms multipiattaforma con gli stili XAML.
zone_pivot_groups: platform
ms.topic: quickstart
ms.prod: xamarin
ms.assetid: CCCF8E57-D021-4542-8709-5808570FC26A
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/02/2019
ms.openlocfilehash: a7e1cdd59b463c38be1a49e962112cb893eed50f
ms.sourcegitcommit: 3f0e4f10e5def19122588bb05f26ab2baa9df6eb
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/23/2020
ms.locfileid: "75488868"
---
# <a name="style-a-cross-platform-xamarinforms-application"></a>Definire lo stile di un'applicazione Xamarin.Forms multipiattaforma

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/getstarted-notes-styled/)

In questo argomento di avvio rapido si apprenderà come:

- Definire lo stile di un'applicazione Xamarin.Forms usando gli stili XAML.

Questo argomento di avvio rapido illustra come definire lo stile di un'applicazione Xamarin.Forms multipiattaforma con gli stili XAML. Il risultato è riportato di seguito:

[![](styling-images/screenshots1-sml.png "Notes Page")](styling-images/screenshots1.png#lightbox "Notes Page")
[![](styling-images/screenshots2-sml.png "Note Entry Page")](styling-images/screenshots2.png#lightbox "Note Entry Page")

### <a name="prerequisites"></a>Prerequisiti

È consigliabile completare l'argomento di [avvio rapido precedente](database.md) prima di provare con questo. In alternativa, scaricare l'[esempio di avvio rapido precedente](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/getstarted-notes-database/) e usarlo come punto di partenza per questo avvio rapido.

::: zone pivot="windows"

## <a name="update-the-app-with-visual-studio"></a>Aggiornare l'app con Visual Studio

1. Avviare Visual Studio e aprire la soluzione Notes.

2. Fare doppio clic su **App.xaml** nel progetto **Notes** in **Esplora soluzioni** per aprire il file, quindi sostituire il codice esistente con il seguente:

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

    Questo codice definisce un valore [`Thickness`](xref:Xamarin.Forms.Thickness), una serie di valori [`Color`](xref:Xamarin.Forms.Color) e stili impliciti per [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) e [`ContentPage`](xref:Xamarin.Forms.ContentPage). Si noti che questi stili, disponibili nella classe [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) a livello di applicazione, possono essere utilizzati in tutta l'applicazione. Per altre informazioni sulla definizione di stili XAML, vedere [Stile](deepdive.md#styling) in [Approfondimenti per la guida di avvio rapido di Xamarin.Forms](deepdive.md).

    Salvare le modifiche apportate ad **App.xaml** premendo **CTRL+S** e chiudere il file.

3. Fare doppio clic su **NotesPage.xaml** nel progetto **Notes** in **Esplora soluzioni** per aprire il file, quindi sostituire il codice esistente con il seguente:

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

    Questo codice aggiunge uno stile implicito per la classe [`ListView`](xref:Xamarin.Forms.ListView) alla classe [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) a livello di pagina e imposta la proprietà `ListView.Margin` su un valore definito nella classe `ResourceDictionary` a livello di applicazione. Si noti che lo stile `ListView` implicito è stato aggiunto alla classe `ResourceDictionary` a livello di pagina, perché viene utilizzato solo da `NotesPage`. Per altre informazioni sulla definizione di stili XAML, vedere [Stile](deepdive.md#styling) in [Approfondimenti per la guida di avvio rapido di Xamarin.Forms](deepdive.md).

    Salvare le modifiche apportate a **NotesPage.xaml** premendo **CTRL+S** e chiudere il file.

4. Fare doppio clic su **NoteEntryPage.xaml** nel progetto **Notes** in **Esplora soluzioni** per aprire il file, quindi sostituire il codice esistente con il seguente:

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

    Questo codice aggiunge stili impliciti per le visualizzazioni [`Editor`](xref:Xamarin.Forms.Editor) e [`Button`](xref:Xamarin.Forms.Button) alla classe [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) a livello di pagina e imposta la proprietà `StackLayout.Margin` su un valore definito nella classe `ResourceDictionary` a livello di applicazione. Si noti che gli stili `Editor` e`Button` impliciti sono stati aggiunti alla classe `ResourceDictionary` a livello di pagina, perché vengono utilizzati solo da `NoteEntryPage`. Per altre informazioni sulla definizione di stili XAML, vedere [Stile](deepdive.md#styling) in [Approfondimenti per la guida di avvio rapido di Xamarin.Forms](deepdive.md).

    Salvare le modifiche apportate a **NoteEntryPage.xaml** premendo **CTRL+S** e chiudere il file.

5. Compilare ed eseguire il progetto in ogni piattaforma. Per altre informazioni, vedere [Compilazione dell'avvio rapido](single-page.md#building-the-quickstart).

    In **NotesPage** premere il pulsante **+** per passare a **NoteEntryPage** e immettere una nota. Si noti come in ogni pagina lo stile sia stato modificato rispetto all'argomento di avvio rapido precedente.

::: zone-end
::: zone pivot="macos"

## <a name="update-the-app-with-visual-studio-for-mac"></a>Aggiornare l'app con Visual Studio per Mac

1. Avviare Visual Studio per Mac e aprire il progetto Notes.

2. Fare doppio clic su **App.xaml** nel progetto **Notes** nel **riquadro della soluzione** per aprire il file, quindi sostituire il codice esistente con il seguente:

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

    Questo codice definisce un valore [`Thickness`](xref:Xamarin.Forms.Thickness), una serie di valori [`Color`](xref:Xamarin.Forms.Color) e stili impliciti per [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) e [`ContentPage`](xref:Xamarin.Forms.ContentPage). Si noti che questi stili, disponibili nella classe [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) a livello di applicazione, possono essere utilizzati in tutta l'applicazione. Per altre informazioni sulla definizione di stili XAML, vedere [Stile](deepdive.md#styling) in [Approfondimenti per la guida di avvio rapido di Xamarin.Forms](deepdive.md).

    Salvare le modifiche apportate ad **App.xaml** scegliendo **File > Salva** o premendo **&#8984; + S** e chiudere il file.

3. Fare doppio clic su **NotesPage.xaml** nel progetto **Notes** nel **riquadro della soluzione** per aprire il file, quindi sostituire il codice esistente con il seguente:

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

    Questo codice aggiunge uno stile implicito per la classe [`ListView`](xref:Xamarin.Forms.ListView) alla classe [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) a livello di pagina e imposta la proprietà `ListView.Margin` su un valore definito nella classe `ResourceDictionary` a livello di applicazione. Si noti che lo stile `ListView` implicito è stato aggiunto alla classe `ResourceDictionary` a livello di pagina, perché viene utilizzato solo da `NotesPage`. Per altre informazioni sulla definizione di stili XAML, vedere [Stile](deepdive.md#styling) in [Approfondimenti per la guida di avvio rapido di Xamarin.Forms](deepdive.md).

    Salvare le modifiche apportate a **NotesPage.xaml** scegliendo **File > Salva** o premendo **&#8984; + S** e chiudere il file.

4. Fare doppio clic su **NoteEntryPage.xaml** nel progetto **Notes** nel **riquadro della soluzione** per aprire il file, quindi sostituire il codice esistente con il seguente:

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

    Questo codice aggiunge stili impliciti per le visualizzazioni [`Editor`](xref:Xamarin.Forms.Editor) e [`Button`](xref:Xamarin.Forms.Button) alla classe [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) a livello di pagina e imposta la proprietà `StackLayout.Margin` su un valore definito nella classe `ResourceDictionary` a livello di applicazione. Si noti che gli stili `Editor` e`Button` impliciti sono stati aggiunti alla classe `ResourceDictionary` a livello di pagina, perché vengono utilizzati solo da `NoteEntryPage`. Per altre informazioni sulla definizione di stili XAML, vedere [Stile](deepdive.md#styling) in [Approfondimenti per la guida di avvio rapido di Xamarin.Forms](deepdive.md).

    Salvare le modifiche apportate a **NoteEntryPage.xaml** scegliendo **File > Salva** o premendo **&#8984; + S** e chiudere il file.

5. Compilare ed eseguire il progetto in ogni piattaforma. Per altre informazioni, vedere [Compilazione dell'avvio rapido](single-page.md#building-the-quickstart).

    In **NotesPage** premere il pulsante **+** per passare a **NoteEntryPage** e immettere una nota. Si noti come in ogni pagina lo stile sia stato modificato rispetto all'argomento di avvio rapido precedente.

::: zone-end

## <a name="next-steps"></a>Passaggi successivi

In questo argomento di avvio rapido si è appreso come:

- Definire lo stile di un'applicazione Xamarin.Forms usando gli stili XAML.

Per altre informazioni sui concetti fondamentali dello sviluppo di applicazioni con Xamarin.Forms, passare agli approfondimenti per la guida di avvio rapido.

> [!div class="nextstepaction"]
> [avanti](deepdive.md)

## <a name="related-links"></a>Collegamenti correlati

- [Notes (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/getstarted-notes-styled/)
- [Approfondimenti per la guida di avvio rapido di Xamarin.Forms](deepdive.md)
