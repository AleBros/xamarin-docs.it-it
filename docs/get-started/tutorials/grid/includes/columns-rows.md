---
ms.openlocfilehash: d87289e481b69592b68627d053e937856d3d6067
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61375388"
---
# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. In **MainPage.xaml** modificare la dichiarazione [`Grid`](xref:Xamarin.Forms.Grid) per definire le colonne e le righe, quindi posizionare il contenuto nelle colonne e nelle righe:

    ```xaml
    <Grid Margin="20,35,20,20">
        <Grid.ColumnDefinitions>
            <ColumnDefinition Width="0.5*" />
            <ColumnDefinition Width="0.5*" />
        </Grid.ColumnDefinitions>
        <Grid.RowDefinitions>
            <RowDefinition Height="50" />
            <RowDefinition Height="50" />
        </Grid.RowDefinitions>
        <Label Text="Column 0, Row 0" />
        <Label Grid.Column="1"
               Text="Column 1, Row 0" />
        <Label Grid.Row="1"
               Text="Column 0, Row 1" />
        <Label Grid.Column="1"
               Grid.Row="1"
               Text="Column 1, Row 1" />
    </Grid>
    ```

    Questo codice definisce le colonne e le righe per [`Grid`](xref:Xamarin.Forms.Grid) e posiziona le istanze di [`Label`](xref:Xamarin.Forms.Label) in colonne e righe specifiche. I dati delle colonne e delle righe sono archiviati nelle proprietà [`ColumnDefinitions`](xref:Xamarin.Forms.Grid.ColumnDefinitions) e [`RowDefinitions`](xref:Xamarin.Forms.Grid.RowDefinitions), che sono rispettivamente raccolte di oggetti [`ColumnDefinition`](xref:Xamarin.Forms.ColumnDefinition) e [`RowDefinition`](xref:Xamarin.Forms.RowDefinition). La larghezza di ogni `ColumnDefinition` è impostata dalla proprietà [`ColumnDefinition.Width`](xref:Xamarin.Forms.ColumnDefinition.Width) e l'altezza di ogni `RowDefinition` è impostata dalla proprietà [`RowDefinition.Height`](xref:Xamarin.Forms.RowDefinition.Height). I valori di larghezza e altezza validi sono:

    - [`Auto`](xref:Xamarin.Forms.GridUnitType.Auto), che consente di adattare le dimensioni di colonna o riga al contenuto.
    - Valori proporzionali, che consentono di ridimensionare le colonne e le righe in proporzione allo spazio rimanente. I valori proporzionali sono indicati dalla terminazione `*`.
    - Valori assoluti, che consentono di ridimensionare le colonne o le righe con valori fissi specifici.

    Nel codice precedente, pertanto, ogni colonna ha una larghezza pari a metà di [`Grid`](xref:Xamarin.Forms.Grid), mentre ogni riga ha un'altezza pari a 50 unità indipendenti dal dispositivo.

    La posizione di ogni [`Label`](xref:Xamarin.Forms.Label) in [`Grid`](xref:Xamarin.Forms.Grid) viene specificata con le proprietà associate [`Grid.Column`](xref:Xamarin.Forms.Grid.ColumnProperty) e [`Grid.Row`](xref:Xamarin.Forms.Grid.RowProperty) tramite un indice in base zero. Pertanto, la prima colonna è la colonna 0 e la prima riga è la riga 0. Per il primo elemento `Label` queste proprietà associate non sono disponibili, perché il rendering di qualsiasi visualizzazione figlio che non le imposta verrà eseguito automaticamente nella colonna 0, riga 0.

    > [!NOTE]
    > La spaziatura tra le colonne e le righe in un elemento [`Grid`](xref:Xamarin.Forms.Grid) può essere impostata con le proprietà [`ColumnSpacing`](xref:Xamarin.Forms.Grid.ColumnSpacing) e [`RowSpacing`](xref:Xamarin.Forms.Grid.RowSpacing). Per altre informazioni, vedere [Spaziatura](~/xamarin-forms/user-interface/layouts/grid.md#spacing) nella guida [Grid di Xamarin.Forms](~/xamarin-forms/user-interface/layouts/grid.md).

1. Nella barra degli strumenti di Visual Studio premere il pulsante **Avvia**, ovvero il pulsante a forma di triangolo simile a un pulsante di riproduzione, per avviare l'applicazione all'interno del simulatore iOS remoto o dell'emulatore Android prescelto:

    [![Screenshot di un elemento Grid con contenuto disposto in colonne e righe, in iOS e Android](../images/columns-rows.png "Elemento Grid con contenuto disposto in colonne e righe")](../images/columns-rows-large.png#lightbox "Elemento Grid con contenuto disposto in colonne e righe")

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

1. In **MainPage.xaml** modificare la dichiarazione [`Grid`](xref:Xamarin.Forms.Grid) per definire le colonne e le righe, quindi posizionare il contenuto nelle colonne e nelle righe:

    ```xaml
    <Grid Margin="20,35,20,20">
        <Grid.ColumnDefinitions>
            <ColumnDefinition Width="0.5*" />
            <ColumnDefinition Width="0.5*" />
        </Grid.ColumnDefinitions>
        <Grid.RowDefinitions>
            <RowDefinition Height="50" />
            <RowDefinition Height="50" />
        </Grid.RowDefinitions>
        <Label Text="Column 0, Row 0" />
        <Label Grid.Column="1"
               Text="Column 1, Row 0" />
        <Label Grid.Row="1"
               Text="Column 0, Row 1" />
        <Label Grid.Column="1"
               Grid.Row="1"
               Text="Column 1, Row 1" />
    </Grid>
    ```

    Questo codice definisce le colonne e le righe per [`Grid`](xref:Xamarin.Forms.Grid) e posiziona le istanze di [`Label`](xref:Xamarin.Forms.Label) in colonne e righe specifiche. I dati delle colonne e delle righe sono archiviati nelle proprietà [`ColumnDefinitions`](xref:Xamarin.Forms.Grid.ColumnDefinitions) e [`RowDefinitions`](xref:Xamarin.Forms.Grid.RowDefinitions), che sono rispettivamente raccolte di oggetti [`ColumnDefinition`](xref:Xamarin.Forms.ColumnDefinition) e [`RowDefinition`](xref:Xamarin.Forms.RowDefinition). La larghezza di ogni `ColumnDefinition` è impostata dalla proprietà [`ColumnDefinition.Width`](xref:Xamarin.Forms.ColumnDefinition.Width) e l'altezza di ogni `RowDefinition` è impostata dalla proprietà [`RowDefinition.Height`](xref:Xamarin.Forms.RowDefinition.Height). I valori di larghezza e altezza validi sono:

    - [`Auto`](xref:Xamarin.Forms.GridUnitType.Auto), che consente di adattare le dimensioni di colonna o riga al contenuto.
    - Valori proporzionali, che consentono di ridimensionare le colonne e le righe in proporzione allo spazio rimanente. I valori proporzionali sono indicati dalla terminazione `*`.
    - Valori assoluti, che consentono di ridimensionare le colonne o le righe con valori fissi specifici.

    Nel codice precedente, pertanto, ogni colonna ha una larghezza pari a metà di [`Grid`](xref:Xamarin.Forms.Grid), mentre ogni riga ha un'altezza pari a 50 unità indipendenti dal dispositivo.

    La posizione di ogni [`Label`](xref:Xamarin.Forms.Label) in [`Grid`](xref:Xamarin.Forms.Grid) viene specificata con le proprietà associate [`Grid.Column`](xref:Xamarin.Forms.Grid.ColumnProperty) e [`Grid.Row`](xref:Xamarin.Forms.Grid.RowProperty) tramite un indice in base zero. Pertanto, la prima colonna è la colonna 0 e la prima riga è la riga 0. Per il primo elemento `Label` queste proprietà associate non sono disponibili, perché il rendering di qualsiasi visualizzazione figlio che non le imposta verrà eseguito automaticamente nella colonna 0, riga 0.

    > [!NOTE]
    > La spaziatura tra le colonne e le righe in un elemento [`Grid`](xref:Xamarin.Forms.Grid) può essere impostata con le proprietà [`ColumnSpacing`](xref:Xamarin.Forms.Grid.ColumnSpacing) e [`RowSpacing`](xref:Xamarin.Forms.Grid.RowSpacing). Per altre informazioni, vedere [Spaziatura](~/xamarin-forms/user-interface/layouts/grid.md#spacing) nella guida [Grid di Xamarin.Forms](~/xamarin-forms/user-interface/layouts/grid.md).

1. Nella barra degli strumenti di Visual Studio per Mac premere il pulsante **Avvia**, ovvero il pulsante a forma di triangolo simile a un pulsante di riproduzione, per avviare l'applicazione all'interno del simulatore iOS o dell'emulatore Android prescelto:

    [![Screenshot di un elemento Grid con contenuto disposto in colonne e righe, in iOS e Android](../images/columns-rows.png "Elemento Grid con contenuto disposto in colonne e righe")](../images/columns-rows-large.png#lightbox "Elemento Grid con contenuto disposto in colonne e righe")
