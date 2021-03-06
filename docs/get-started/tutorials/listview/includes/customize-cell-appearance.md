---
ms.openlocfilehash: 04d2a244482c92a615c3eec2130cec1df8aaaf1f
ms.sourcegitcommit: 89b3e383a37db5b940f0c63bbfe9cb806dc7d5d1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/15/2020
ms.locfileid: "81389921"
---
In precedenza [`ListView`](xref:Xamarin.Forms.ListView) veniva popolata con i dati tramite data binding. Tuttavia, nonostante il data binding in una raccolta in cui ogni oggetto definiva più elementi di dati, veniva visualizzato solo un singolo elemento di dati per ogni oggetto (proprietà `Name` dell'oggetto `Monkey`).

In questo esercizio il progetto **ListViewTutorial** verrà modificato in modo che [`ListView`](xref:Xamarin.Forms.ListView) visualizzi più elementi di dati in ogni riga.

# <a name="visual-studio"></a>[Visual Studio](#tab/vswin)

1. In **MainPage.xaml** modificare la dichiarazione [`ListView`](xref:Xamarin.Forms.ListView) per personalizzare l'aspetto di ogni riga:

    ```xaml
    <ListView ItemsSource="{Binding Monkeys}"
              HasUnevenRows="true"
              ItemSelected="OnListViewItemSelected"
              ItemTapped="OnListViewItemTapped">
        <ListView.ItemTemplate>
            <DataTemplate>
                <ViewCell>
                    <Grid Padding="10">
                        <Grid.RowDefinitions>
                            <RowDefinition Height="Auto" />
                            <RowDefinition Height="*" />
                        </Grid.RowDefinitions>
                        <Grid.ColumnDefinitions>
                            <ColumnDefinition Width="Auto" />
                            <ColumnDefinition Width="*" />
                        </Grid.ColumnDefinitions>
                        <Image Grid.RowSpan="2"
                               Source="{Binding ImageUrl}"
                               Aspect="AspectFill"
                               HeightRequest="60"
                               WidthRequest="60" />
                        <Label Grid.Column="1"
                               Text="{Binding Name}"
                               FontAttributes="Bold" />
                        <Label Grid.Row="1"
                               Grid.Column="1"
                               Text="{Binding Location}"
                               VerticalOptions="End" />
                    </Grid>
                </ViewCell>
            </DataTemplate>
        </ListView.ItemTemplate>
    </ListView>
    ```

    Questo codice imposta la proprietà [`ListView.ItemTemplate`](xref:Xamarin.Forms.ItemsView`1.ItemTemplate) su [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) che definisce l'aspetto di ogni riga in [`ListView`](xref:Xamarin.Forms.ListView). L'elemento figlio di `DataTemplate` deve derivare o essere di tipo [`Cell`](xref:Xamarin.Forms.Cell). Questo codice usa una classe [`ViewCell`](xref:Xamarin.Forms.ViewCell), che deriva da `Cell`, per creare un layout personalizzato per ogni riga di `ListView`. Il layout all'interno di `ViewCell` è gestito qui tramite [`Grid`](xref:Xamarin.Forms.Grid) che contiene un oggetto [`Image`](xref:Xamarin.Forms.Image) e due oggetti [`Label`](xref:Xamarin.Forms.Label). I dati dell'oggetto `Image` associano la relativa proprietà [`Source`](xref:Xamarin.Forms.Image.Source) alla proprietà `ImageUrl` di ogni oggetto `Monkey`, mentre gli oggetti `Label` associano le relative proprietà [`Text`](xref:Xamarin.Forms.Label.Text) alle proprietà `Name` e `Location` di ogni oggetto `Monkey`.

    Per impostazione predefinita, tutte le righe in [`ListView`](xref:Xamarin.Forms.ListView) hanno la stessa altezza. Tuttavia, poiché questo codice imposta la proprietà [`ListView.HasUnevenRows`](xref:Xamarin.Forms.ListView.HasUnevenRows) su `true`, l'altezza della riga può essere adattata al contenuto. In questo modo vengono adattate le proprietà `Name` e `Location` di ogni oggetto `Monkey` con stringhe di lunghezza variabile.

    Per altre informazioni sull'aspetto delle celle di [`ListView`](xref:Xamarin.Forms.ListView), vedere [Personalizzazione dell'aspetto delle celle di ListView](~/xamarin-forms/user-interface/listview/customizing-cell-appearance.md). Per altre informazioni sui modelli di dati, vedere [Modelli di dati Xamarin.Forms](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md).

1. Nella barra degli strumenti di Visual Studio premere il pulsante **Avvia**, ovvero il pulsante a forma di triangolo simile a un pulsante di riproduzione, per avviare l'applicazione all'interno del simulatore iOS remoto o dell'emulatore Android prescelto:

    [![Screenshot di un controllo ListView i cui elementi sono basati su un modello di dati](../images/customize-cell-appearance.png "ListView che visualizza i dati basati su modello")](../images/customize-cell-appearance-large.png#lightbox "ListView che visualizza i dati basati su modello")

# <a name="visual-studio-for-mac"></a>[Visual Studio per Mac](#tab/vsmac)

1. In **MainPage.xaml** modificare la dichiarazione [`ListView`](xref:Xamarin.Forms.ListView) per personalizzare l'aspetto di ogni riga:

    ```xaml
    <ListView ItemsSource="{Binding Monkeys}"
              HasUnevenRows="true"
              ItemSelected="OnListViewItemSelected"
              ItemTapped="OnListViewItemTapped">
        <ListView.ItemTemplate>
            <DataTemplate>
                <ViewCell>
                    <Grid Padding="10">
                        <Grid.RowDefinitions>
                            <RowDefinition Height="Auto" />
                            <RowDefinition Height="*" />
                        </Grid.RowDefinitions>
                        <Grid.ColumnDefinitions>
                            <ColumnDefinition Width="Auto" />
                            <ColumnDefinition Width="*" />
                        </Grid.ColumnDefinitions>
                        <Image Grid.RowSpan="2"
                               Source="{Binding ImageUrl}"
                               Aspect="AspectFill"
                               HeightRequest="60"
                               WidthRequest="60" />
                        <Label Grid.Column="1"
                               Text="{Binding Name}"
                               FontAttributes="Bold" />
                        <Label Grid.Row="1"
                               Grid.Column="1"
                               Text="{Binding Location}"
                               VerticalOptions="End" />
                    </Grid>
                </ViewCell>
            </DataTemplate>
        </ListView.ItemTemplate>
    </ListView>
    ```

    Questo codice imposta la proprietà [`ListView.ItemTemplate`](xref:Xamarin.Forms.ItemsView`1.ItemTemplate) su [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) che definisce l'aspetto di ogni riga in [`ListView`](xref:Xamarin.Forms.ListView). L'elemento figlio di `DataTemplate` deve derivare o essere di tipo [`Cell`](xref:Xamarin.Forms.Cell). Questo codice usa una classe [`ViewCell`](xref:Xamarin.Forms.ViewCell), che deriva da `Cell`, per creare un layout personalizzato per ogni riga di `ListView`. Il layout all'interno di `ViewCell` è gestito qui tramite [`Grid`](xref:Xamarin.Forms.Grid) che contiene un oggetto [`Image`](xref:Xamarin.Forms.Image) e due oggetti [`Label`](xref:Xamarin.Forms.Label). I dati dell'oggetto `Image` associano la relativa proprietà [`Source`](xref:Xamarin.Forms.Image.Source) alla proprietà `ImageUrl` di ogni oggetto `Monkey`, mentre gli oggetti `Label` associano le relative proprietà [`Text`](xref:Xamarin.Forms.Label.Text) alle proprietà `Name` e `Location` di ogni oggetto `Monkey`.

    Per impostazione predefinita, tutte le righe in [`ListView`](xref:Xamarin.Forms.ListView) hanno la stessa altezza. Tuttavia, poiché questo codice imposta la proprietà [`ListView.HasUnevenRows`](xref:Xamarin.Forms.ListView.HasUnevenRows) su `true`, l'altezza della riga può essere adattata al contenuto. In questo modo vengono adattate le proprietà `Name` e `Location` di ogni oggetto `Monkey` con stringhe di lunghezza variabile.

    Per altre informazioni sull'aspetto delle celle di [`ListView`](xref:Xamarin.Forms.ListView), vedere [Personalizzazione dell'aspetto delle celle di ListView](~/xamarin-forms/user-interface/listview/customizing-cell-appearance.md). Per altre informazioni sui modelli di dati, vedere [Modelli di dati Xamarin.Forms](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md).

1. Nella barra degli strumenti di Visual Studio per Mac premere il pulsante **Avvia**, ovvero il pulsante a forma di triangolo simile a un pulsante di riproduzione, per avviare l'applicazione all'interno del simulatore iOS o dell'emulatore Android prescelto:

    [![Screenshot di un controllo ListView i cui elementi sono basati su un modello di dati](../images/customize-cell-appearance.png "ListView che visualizza i dati basati su modello")](../images/customize-cell-appearance-large.png#lightbox "ListView che visualizza i dati basati su modello")
