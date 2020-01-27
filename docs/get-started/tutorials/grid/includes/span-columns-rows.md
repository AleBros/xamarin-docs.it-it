---
ms.openlocfilehash: c826ee87c006b05322af8c9312bdf3120df8b357
ms.sourcegitcommit: 3f0e4f10e5def19122588bb05f26ab2baa9df6eb
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/23/2020
ms.locfileid: "61375359"
---
# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. In **MainPage.xaml**, modificare la dichiarazione [`Grid`](xref:Xamarin.Forms.Grid) per definire colonne e righe e posizionare il contenuto che si estende su colonne e righe:

    ```xaml
    <Grid Margin="20,35,20,20">
        <Grid.ColumnDefinitions>
            <ColumnDefinition Width="0.5*" />
            <ColumnDefinition Width="0.5*" />
        </Grid.ColumnDefinitions>
        <Grid.RowDefinitions>
            <RowDefinition Height="50" />
            <RowDefinition Height="30" />
            <RowDefinition Height="30" />
        </Grid.RowDefinitions>
        <Label Grid.ColumnSpan="2"
               Text="This text uses the ColumnSpan property to span both columns." />
        <Label Grid.Row="1"
               Grid.RowSpan="2"
               Text="This text uses the RowSpan property to span two rows." />
    </Grid>
    ```

    Questo codice definisce le colonne e le righe per [`Grid`](xref:Xamarin.Forms.Grid) e posiziona le istanze di [`Label`](xref:Xamarin.Forms.Label) in colonne e righe specifiche. Il primo `Label` imposta la proprietà associata [`ColumnSpan`](xref:Xamarin.Forms.Grid.ColumnSpanProperty) in modo che il relativo testo si estenda su più colonne. La proprietà `ColumnSpan` è impostata su 2, il che rappresenta il numero di colonne su cui si espande `Label`. Il secondo `Label` imposta la proprietà associata [`RowSpan`](xref:Xamarin.Forms.Grid.RowSpanProperty) in modo che il relativo testo si estenda su più righe. La proprietà `RowSpan` è impostata su 2, il che rappresenta il numero di righe su cui si espande `Label`.

1. Nella barra degli strumenti di Visual Studio premere il pulsante **Avvia**, ovvero il pulsante a forma di triangolo simile a un pulsante di riproduzione, per avviare l'applicazione all'interno del simulatore remoto iOS o dell'emulatore Android prescelto:

    [![Screenshot di una griglia con contenuto che si estende su più colonne e righe, in iOS e Android](../images/span-columns-rows.png "Griglia con contenuto che si estende in colonne e righe")](../images/span-columns-rows-large.png#lightbox "Griglia con contenuto che si estende in colonne e righe")

    Per altre informazioni sull'espansione su più righe e colonne, vedere [Estensione](~/xamarin-forms/user-interface/layouts/grid.md#spans) nella guida [Griglia Xamarin.Forms](~/xamarin-forms/user-interface/layouts/grid.md).

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

1. In **MainPage.xaml**, modificare la dichiarazione [`Grid`](xref:Xamarin.Forms.Grid) per definire colonne e righe e posizionare il contenuto che si estende su colonne e righe:

    ```xaml
    <Grid Margin="20,35,20,20">
        <Grid.ColumnDefinitions>
            <ColumnDefinition Width="0.5*" />
            <ColumnDefinition Width="0.5*" />
        </Grid.ColumnDefinitions>
        <Grid.RowDefinitions>
            <RowDefinition Height="50" />
            <RowDefinition Height="30" />
            <RowDefinition Height="30" />
        </Grid.RowDefinitions>
        <Label Grid.ColumnSpan="2"
               Text="This text uses the ColumnSpan property to span both columns." />
        <Label Grid.Row="1"
               Grid.RowSpan="2"
               Text="This text uses the RowSpan property to span two rows." />
    </Grid>
    ```

    Questo codice definisce le colonne e le righe per [`Grid`](xref:Xamarin.Forms.Grid) e posiziona le istanze di [`Label`](xref:Xamarin.Forms.Label) in colonne e righe specifiche. Il primo `Label` imposta la proprietà associata [`ColumnSpan`](xref:Xamarin.Forms.Grid.ColumnSpanProperty) in modo che il relativo testo si estenda su più colonne. La proprietà `ColumnSpan` è impostata su 2, il che rappresenta il numero di colonne su cui si espande `Label`. Il secondo `Label` imposta la proprietà associata [`RowSpan`](xref:Xamarin.Forms.Grid.RowSpanProperty) in modo che il relativo testo si estenda su più righe. La proprietà `RowSpan` è impostata su 2, il che rappresenta il numero di righe su cui si espande `Label`.

1. Nella barra degli strumenti di Visual Studio per Mac premere il pulsante **Avvia**, ovvero il pulsante a forma di triangolo simile a un pulsante di riproduzione, per avviare l'applicazione all'interno del simulatore iOS o dell'emulatore Android prescelto:

    [![Screenshot di una griglia con contenuto che si estende su più colonne e righe, in iOS e Android](../images/span-columns-rows.png "Griglia con contenuto che si estende in colonne e righe")](../images/span-columns-rows-large.png#lightbox "Griglia con contenuto che si estende in colonne e righe")

    Per altre informazioni sull'espansione su più righe e colonne, vedere [Estensione](~/xamarin-forms/user-interface/layouts/grid.md#spans) nella guida [Griglia Xamarin.Forms](~/xamarin-forms/user-interface/layouts/grid.md).
