---
ms.openlocfilehash: c33db7de63a585cb6fb47aa1145aa724c9eb790f
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61037559"
---
# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. In **MainPage.xaml** modificare la dichiarazione [`ListView`](xref:Xamarin.Forms.ListView) in modo che imposti gestori per gli eventi [`ItemSelected`](xref:Xamarin.Forms.ListView.ItemSelected) e [`ItemTapped`](xref:Xamarin.Forms.ListView.ItemTapped):

    ```xaml
    <ListView ItemsSource="{Binding Monkeys}"
              ItemSelected="OnListViewItemSelected"
              ItemTapped="OnListViewItemTapped" />
    ```

    Questo codice imposta l'evento [`ItemSelected`](xref:Xamarin.Forms.ListView.ItemSelected) su un gestore eventi denominato `OnListViewItemSelected` e l'evento [`ItemTapped`](xref:Xamarin.Forms.ListView.ItemTapped) su un gestore eventi denominato `OnListViewItemTapped`. Entrambi i gestori eventi verranno creati nel passaggio successivo.

1. In **Esplora soluzioni**, nel progetto **ListViewTutorial**, espandere **MainPage.xaml** e fare doppio clic su **MainPage.xaml.cs** per aprirlo. In **MainPage.xaml.cs** aggiungere i gestori eventi `OnListViewItemSelected` e `OnListViewItemTapped` alla classe:

    ```csharp
    void OnListViewItemSelected(object sender, SelectedItemChangedEventArgs e)
    {
        Monkey selectedItem = e.SelectedItem as Monkey;
    }

    void OnListViewItemTapped(object sender, ItemTappedEventArgs e)
    {
        Monkey tappedItem = e.Item as Monkey;
    }
    ```

    Quando una voce dell'elemento [`ListView`](xref:Xamarin.Forms.ListView) viene toccata, sono attivati gli eventi [`ItemSelected`](xref:Xamarin.Forms.ListView.ItemSelected) e [`ItemTapped`](xref:Xamarin.Forms.ListView.ItemTapped) che eseguono rispettivamente i metodi `OnListViewItemSelected` e `OnListItemTapped`. L'argomento `sender` di entrambi i metodi è l'oggetto `ListView` responsabile dell'attivazione dell'evento e può essere usato per accedere all'oggetto `ListView`. L'argomento [`SelectedItemChangedEventArgs`](xref:Xamarin.Forms.SelectedItemChangedEventArgs) nel metodo `OnListViewItemSelected` fornisce la voce selezionata e l'argomento [`ItemTappedEventArgs`](xref:Xamarin.Forms.ItemTappedEventArgs) nel metodo `OnListViewItemTapped` fornisce la voce toccata.

    > [!IMPORTANT]
    > L'evento [`ItemSelected`](xref:Xamarin.Forms.ListView.ItemSelected) è attivato solo quando viene selezionata una nuova voce nell'elemento [`ListView`](xref:Xamarin.Forms.ListView). Pertanto, toccando due volte la stessa voce saranno generati due eventi [`ItemTapped`](xref:Xamarin.Forms.ListView.ItemTapped), ma un solo evento `ItemSelected`.

1. Nella barra degli strumenti di Visual Studio premere il pulsante **Avvia**, ovvero il pulsante a forma di triangolo simile a un pulsante di riproduzione, per avviare l'applicazione all'interno del simulatore iOS remoto o dell'emulatore Android prescelto:

    [![Screenshot di un elemento ListView che risponde alla selezione e al tocco di voci in iOS e Android](../images/item-selection.png "Selezione di una voce in un elemento ListView")](../images/item-selection-large.png#lightbox "Selezione di una voce in un elemento ListView")

    Impostare punti di interruzione nei due gestori eventi e toccare voci nell'elemento [`ListView`](xref:Xamarin.Forms.ListView). Si noti che l'evento [`ItemSelected`](xref:Xamarin.Forms.ListView.ItemSelected) è attivato solo quando una nuova voce viene selezionata nell'elemento [`ListView`](xref:Xamarin.Forms.ListView), mentre l'evento [`ItemTapped`](xref:Xamarin.Forms.ListView.ItemTapped) viene generato ogni volta che una voce viene toccata.

    Per altre informazioni sulla selezione e il tocco di voci, vedere [Selection & Taps](~/xamarin-forms/user-interface/listview/interactivity.md#selection--taps) (Selezione e tocco) nella guida [ListView Interactivity](~/xamarin-forms/user-interface/listview/interactivity.md) (Interattività di ListView).

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

1. In **MainPage.xaml** modificare la dichiarazione [`ListView`](xref:Xamarin.Forms.ListView) in modo che imposti gestori per gli eventi [`ItemSelected`](xref:Xamarin.Forms.ListView.ItemSelected) e [`ItemTapped`](xref:Xamarin.Forms.ListView.ItemTapped):

    ```xaml
    <ListView ItemsSource="{Binding Monkeys}"
              ItemSelected="OnListViewItemSelected"
              ItemTapped="OnListViewItemTapped" />
    ```

    Questo codice imposta l'evento [`ItemSelected`](xref:Xamarin.Forms.ListView.ItemSelected) su un gestore eventi denominato `OnListViewItemSelected` e l'evento [`ItemTapped`](xref:Xamarin.Forms.ListView.ItemTapped) su un gestore eventi denominato `OnListViewItemTapped`. Entrambi i gestori eventi verranno creati nel passaggio successivo.

1. Nel **riquadro della soluzione**, nel progetto **ListViewTutorial**, espandere **MainPage.xaml** e fare doppio clic su **MainPage.xaml.cs** per aprirlo. In **MainPage.xaml.cs** aggiungere i gestori eventi `OnListViewItemSelected` e `OnListViewItemTapped` alla classe:

    ```csharp
    void OnListViewItemSelected(object sender, SelectedItemChangedEventArgs e)
    {
        Monkey selectedItem = e.SelectedItem as Monkey;
    }

    void OnListViewItemTapped(object sender, ItemTappedEventArgs e)
    {
        Monkey tappedItem = e.Item as Monkey;
    }
    ```

    Quando una voce dell'elemento [`ListView`](xref:Xamarin.Forms.ListView) viene toccata, sono attivati gli eventi [`ItemSelected`](xref:Xamarin.Forms.ListView.ItemSelected) e [`ItemTapped`](xref:Xamarin.Forms.ListView.ItemTapped) che eseguono rispettivamente i metodi `OnListViewItemSelected` e `OnListItemTapped`. L'argomento `sender` di entrambi i metodi è l'oggetto `ListView` responsabile dell'attivazione dell'evento e può essere usato per accedere all'oggetto `ListView`. L'argomento [`SelectedItemChangedEventArgs`](xref:Xamarin.Forms.SelectedItemChangedEventArgs) nel metodo `OnListViewItemSelected` fornisce la voce selezionata e l'argomento [`ItemTappedEventArgs`](xref:Xamarin.Forms.ItemTappedEventArgs) nel metodo `OnListViewItemTapped` fornisce la voce toccata.

    > [!IMPORTANT]
    > L'evento [`ItemSelected`](xref:Xamarin.Forms.ListView.ItemSelected) è attivato solo quando viene selezionata una nuova voce nell'elemento [`ListView`](xref:Xamarin.Forms.ListView). Pertanto, toccando due volte la stessa voce saranno generati due eventi [`ItemTapped`](xref:Xamarin.Forms.ListView.ItemTapped), ma un solo evento `ItemSelected`.

1. Nella barra degli strumenti di Visual Studio per Mac premere il pulsante **Avvia**, ovvero il pulsante a forma di triangolo simile a un pulsante di riproduzione, per avviare l'applicazione all'interno del simulatore iOS o dell'emulatore Android prescelto:

    [![Screenshot di un elemento ListView che risponde alla selezione e al tocco di voci in iOS e Android](../images/item-selection.png "Selezione di una voce in un elemento ListView")](../images/item-selection-large.png#lightbox "Selezione di una voce in un elemento ListView")

    Impostare punti di interruzione nei due gestori eventi e toccare voci nell'elemento [`ListView`](xref:Xamarin.Forms.ListView). Si noti che l'evento [`ItemSelected`](xref:Xamarin.Forms.ListView.ItemSelected) è attivato solo quando una nuova voce viene selezionata nell'elemento [`ListView`](xref:Xamarin.Forms.ListView), mentre l'evento [`ItemTapped`](xref:Xamarin.Forms.ListView.ItemTapped) viene generato ogni volta che una voce viene toccata.

    Per altre informazioni sulla selezione e il tocco di voci, vedere [Selection & Taps](~/xamarin-forms/user-interface/listview/interactivity.md#selection--taps) (Selezione e tocco) nella guida [ListView Interactivity](~/xamarin-forms/user-interface/listview/interactivity.md) (Interattività di ListView).
