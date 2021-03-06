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
ms.date: 02/07/2020
ms.openlocfilehash: fbd957c68d7a9aa2f8e44c91fab6174d8ed72014
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2020
ms.locfileid: "77068745"
---
# <a name="style-a-cross-platform-xamarinforms-application"></a>Definire lo stile di un'applicazione Xamarin.Forms multipiattaforma

[![Scarica](~/media/shared/download.png) l'esempio Scarica l'esempioDownload Sample Download the sample](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/getstarted-notes-styled/)

In questo argomento di avvio rapido si apprenderà come:

- Definire lo stile di un'applicazione Xamarin.Forms usando gli stili XAML.

Questo argomento di avvio rapido illustra come definire lo stile di un'applicazione Xamarin.Forms multipiattaforma con gli stili XAML. L'applicazione finale è riportata di seguito:

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
            <Color x:Key="AppBackgroundColor">AliceBlue</Color>
            <Color x:Key="NavigationBarColor">#1976D2</Color>
            <Color x:Key="NavigationBarTextColor">White</Color>

            <!-- Implicit styles -->
            <Style TargetType="{x:Type NavigationPage}">
                <Setter Property="BarBackgroundColor"
                        Value="{StaticResource NavigationBarColor}" />
                 <Setter Property="BarTextColor"
                        Value="{StaticResource NavigationBarTextColor}" />           
            </Style>

            <Style TargetType="{x:Type ContentPage}"
                   ApplyToDerivedTypes="True">
                <Setter Property="BackgroundColor"
                        Value="{StaticResource AppBackgroundColor}" />
            </Style>

        </Application.Resources>
    </Application>
    ```

    Questo codice definisce un [`Thickness`](xref:Xamarin.Forms.Thickness) valore, una serie di [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) [`ContentPage`](xref:Xamarin.Forms.ContentPage) [`Color`](xref:Xamarin.Forms.Color) valori e stili impliciti per e . Si noti che questi stili, [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary)che si trovano a livello di applicazione , possono essere utilizzati in tutta l'applicazione. Per altre informazioni sulla definizione di stili XAML, vedere [Stile](deepdive.md#styling) in [Approfondimenti per la guida di avvio rapido di Xamarin.Forms](deepdive.md).

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
                              TextColor="Black"
                              Detail="{Binding Date}" />
                </DataTemplate>
            </ListView.ItemTemplate>
        </ListView>

    </ContentPage>
    ```

    Questo codice aggiunge uno [`ListView`](xref:Xamarin.Forms.ListView) stile implicito [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary)per l'oggetto a livello di pagina e imposta la proprietà su `ListView.Margin` un valore definito in application-level `ResourceDictionary`. Si noti che lo stile `ListView` implicito è stato aggiunto alla classe `ResourceDictionary` a livello di pagina, perché viene utilizzato solo da `NotesPage`. Per altre informazioni sulla definizione di stili XAML, vedere [Stile](deepdive.md#styling) in [Approfondimenti per la guida di avvio rapido di Xamarin.Forms](deepdive.md).

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
                <Setter Property="BackgroundColor" Value="#1976D2" />
                <Setter Property="TextColor" Value="White" />
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

    Questo codice aggiunge stili [`Editor`](xref:Xamarin.Forms.Editor) [`Button`](xref:Xamarin.Forms.Button) impliciti per le [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary)visualizzazioni `StackLayout.Margin` e a livello di pagina `ResourceDictionary`e imposta la proprietà su un valore definito in application-level . Si noti che gli stili `Editor` e`Button` impliciti sono stati aggiunti alla classe `ResourceDictionary` a livello di pagina, perché vengono utilizzati solo da `NoteEntryPage`. Per altre informazioni sulla definizione di stili XAML, vedere [Stile](deepdive.md#styling) in [Approfondimenti per la guida di avvio rapido di Xamarin.Forms](deepdive.md).

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
            <Color x:Key="AppBackgroundColor">AliceBlue</Color>
            <Color x:Key="NavigationBarColor">#1976D2</Color>
            <Color x:Key="NavigationBarTextColor">White</Color>

            <!-- Implicit styles -->
            <Style TargetType="{x:Type NavigationPage}">
                <Setter Property="BarBackgroundColor"
                        Value="{StaticResource NavigationBarColor}" />
                 <Setter Property="BarTextColor"
                        Value="{StaticResource NavigationBarTextColor}" />           
            </Style>

            <Style TargetType="{x:Type ContentPage}"
                   ApplyToDerivedTypes="True">
                <Setter Property="BackgroundColor"
                        Value="{StaticResource AppBackgroundColor}" />
            </Style>

        </Application.Resources>
    </Application>
    ```

    Questo codice definisce un [`Thickness`](xref:Xamarin.Forms.Thickness) valore, una serie di [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) [`ContentPage`](xref:Xamarin.Forms.ContentPage) [`Color`](xref:Xamarin.Forms.Color) valori e stili impliciti per e . Si noti che questi stili, [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary)che si trovano a livello di applicazione , possono essere utilizzati in tutta l'applicazione. Per altre informazioni sulla definizione di stili XAML, vedere [Stile](deepdive.md#styling) in [Approfondimenti per la guida di avvio rapido di Xamarin.Forms](deepdive.md).

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
                              TextColor="Black"
                              Detail="{Binding Date}" />
                </DataTemplate>
            </ListView.ItemTemplate>
        </ListView>

    </ContentPage>
    ```

    Questo codice aggiunge uno [`ListView`](xref:Xamarin.Forms.ListView) stile implicito [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary)per l'oggetto a livello di pagina e imposta la proprietà su `ListView.Margin` un valore definito in application-level `ResourceDictionary`. Si noti che lo stile `ListView` implicito è stato aggiunto alla classe `ResourceDictionary` a livello di pagina, perché viene utilizzato solo da `NotesPage`. Per altre informazioni sulla definizione di stili XAML, vedere [Stile](deepdive.md#styling) in [Approfondimenti per la guida di avvio rapido di Xamarin.Forms](deepdive.md).

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
                <Setter Property="BackgroundColor" Value="#1976D2" />
                <Setter Property="TextColor" Value="White" />
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

    Questo codice aggiunge stili [`Editor`](xref:Xamarin.Forms.Editor) [`Button`](xref:Xamarin.Forms.Button) impliciti per le [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary)visualizzazioni `StackLayout.Margin` e a livello di pagina `ResourceDictionary`e imposta la proprietà su un valore definito in application-level . Si noti che gli stili `Editor` e`Button` impliciti sono stati aggiunti alla classe `ResourceDictionary` a livello di pagina, perché vengono utilizzati solo da `NoteEntryPage`. Per altre informazioni sulla definizione di stili XAML, vedere [Stile](deepdive.md#styling) in [Approfondimenti per la guida di avvio rapido di Xamarin.Forms](deepdive.md).

    Salvare le modifiche apportate a **NoteEntryPage.xaml** scegliendo **File > Salva** o premendo **&#8984; + S** e chiudere il file.

5. Compilare ed eseguire il progetto in ogni piattaforma. Per altre informazioni, vedere [Compilazione dell'avvio rapido](single-page.md#building-the-quickstart).

    In **NotesPage** premere il pulsante **+** per passare a **NoteEntryPage** e immettere una nota. Si noti come in ogni pagina lo stile sia stato modificato rispetto all'argomento di avvio rapido precedente.

::: zone-end

## <a name="next-steps"></a>Passaggi successivi

In questa guida introduttiva si è appreso come:

- Definire lo stile di un'applicazione Xamarin.Forms usando gli stili XAML.

Per altre informazioni sui concetti fondamentali dello sviluppo di applicazioni con Xamarin.Forms, passare agli approfondimenti per la guida di avvio rapido.

> [!div class="nextstepaction"]
> [Avanti](deepdive.md)

## <a name="related-links"></a>Collegamenti correlati

- [Notes (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/getstarted-notes-styled/)
- [Approfondimenti per la guida di avvio rapido di Xamarin.Forms](deepdive.md)
