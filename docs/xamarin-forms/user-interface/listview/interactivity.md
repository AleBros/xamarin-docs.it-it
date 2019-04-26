---
title: Interattività ListView
description: Questo articolo illustra come aggiungere interattività a un ListView di xamarin. Forms mediante l'implementazione di pull per aggiornare le selezioni e le azioni di contesto.
ms.prod: xamarin
ms.assetid: CD14EB90-B08C-4E8F-A314-DA0EEC76E647
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/27/2019
ms.openlocfilehash: 1447526ef925431e3cad5f36d4ce7a528c8ab07c
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61386684"
---
# <a name="listview-interactivity"></a>Interattività ListView

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/ListView/interactivity)

[`ListView`](xref:Xamarin.Forms.ListView) supporta l'interazione con i dati in esso contenute.

<a name="selectiontaps" />

## <a name="selection--taps"></a>Scelte del & selezione

Il [ `ListView` ](xref:Xamarin.Forms.ListView) modalità di selezione è controllata dall'impostazione il [ `ListView.SelectionMode` ](xref:Xamarin.Forms.ListView.SelectionMode) proprietà su un valore di [ `ListViewSelectionMode` ](xref:Xamarin.Forms.ListViewSelectionMode) enumerazione:

- [`Single`](xref:Xamarin.Forms.ListViewSelectionMode.Single) indica che si possa selezionare un singolo elemento, con l'elemento selezionato viene evidenziato. Rappresenta il valore predefinito.
- [`None`](xref:Xamarin.Forms.ListViewSelectionMode.None) indica che non è possibile selezionare elementi.

Quando un utente tocca un elemento, vengono generati due eventi:

- [`ItemSelected`](xref:Xamarin.Forms.ListView.ItemSelected) generato quando viene selezionato un nuovo elemento.
- [`ItemTapped`](xref:Xamarin.Forms.ListView.ItemTapped) generato quando si tocca un elemento.

Toccare due volte lo stesso elemento genereranno due [ `ItemTapped` ](xref:Xamarin.Forms.ListView.ItemTapped) gli eventi, ma verrà attivare solo una singola [ `ItemSelected` ](xref:Xamarin.Forms.ListView.ItemSelected) evento.

> [!NOTE]
> Il [ `ItemTappedEventArgs` ](xref:Xamarin.Forms.ItemTappedEventArgs) classe, che contiene gli argomenti dell'evento per il [ `ItemTapped` ](xref:Xamarin.Forms.ListView.ItemTapped) evento, ha [ `Group` ](xref:Xamarin.Forms.ItemTappedEventArgs.Group) e [ `Item` ](xref:Xamarin.Forms.ItemTappedEventArgs.Item) delle proprietà e un `ItemIndex` proprietà il cui valore rappresenta l'indice nel [ `ListView` ](xref:Xamarin.Forms.ListView) dell'elemento scelte. Allo stesso modo, il [ `SelectedItemChangedEventArgs` ](xref:Xamarin.Forms.SelectedItemChangedEventArgs) classe, che contiene gli argomenti dell'evento per il [ `ItemSelected` ](xref:Xamarin.Forms.ListView.ItemSelected) evento, dispone di un [ `SelectedItem` ](xref:Xamarin.Forms.SelectedItemChangedEventArgs.SelectedItem) proprietà e un `SelectedItemIndex` proprietà il cui valore rappresenta l'indice nel `ListView` dell'elemento selezionato.

Quando la [ `SelectionMode` ](xref:Xamarin.Forms.ListView.SelectionMode) è impostata su [ `Single` ](xref:Xamarin.Forms.ListViewSelectionMode.Single), gli elementi nel [ `ListView` ](xref:Xamarin.Forms.ListView) può essere selezionato, il [ `ItemSelected` ](xref:Xamarin.Forms.ListView.ItemSelected) e [ `ItemTapped` ](xref:Xamarin.Forms.ListView.ItemTapped) verranno generati eventi e il [ `SelectedItem` ](xref:Xamarin.Forms.ListView.SelectedItem) proprietà verrà impostata sul valore dell'elemento selezionato.

Quando la [ `SelectionMode` ](xref:Xamarin.Forms.ListView.SelectionMode) è impostata su [ `None` ](xref:Xamarin.Forms.ListViewSelectionMode.None), gli elementi nel [ `ListView` ](xref:Xamarin.Forms.ListView) non può essere selezionato, il [ `ItemSelected` ](xref:Xamarin.Forms.ListView.ItemSelected) evento non verrà generato e il [ `SelectedItem` ](xref:Xamarin.Forms.ListView.SelectedItem) rimarrà proprietà `null`. Tuttavia [ `ItemTapped` ](xref:Xamarin.Forms.ListView.ItemTapped) verranno comunque generati eventi e l'elemento selezionato verrà evidenziata brevemente durante la scelta.

Quando è stato selezionato un elemento e il [ `SelectionMode` ](xref:Xamarin.Forms.ListView.SelectionMode) proprietà viene modificata da [ `Single` ](xref:Xamarin.Forms.ListViewSelectionMode.Single) al [ `None` ](xref:Xamarin.Forms.ListViewSelectionMode.None), il [ `SelectedItem` ](xref:Xamarin.Forms.ListView.SelectedItem) verrà impostata su `null` e il [ `ItemSelected` ](xref:Xamarin.Forms.ListView.ItemSelected) evento verrà generato con un `null` elemento.

L'esempio di screenshot seguente mostra una [ `ListView` ](xref:Xamarin.Forms.ListView) con la modalità di selezione predefinita:

![](interactivity-images/selection-default.png "ListView con selezione abilitata")

### <a name="disabling-selection"></a>La disabilitazione di selezione

Per disabilitare [ `ListView` ](xref:Xamarin.Forms.ListView) selezione impostato il [ `SelectionMode` ](xref:Xamarin.Forms.ListView.SelectionMode) proprietà [ `None` ](xref:Xamarin.Forms.ListViewSelectionMode.None):

```xaml
<ListView ... SelectionMode="None" />
```

```csharp
var listView = new ListView { ... SelectionMode = ListViewSelectionMode.None };
```

<a name="Context_Actions" />

## <a name="context-actions"></a>Azioni di contesto

Spesso, gli utenti desiderano intervenire su un elemento in un `ListView`. Si consideri ad esempio un elenco di messaggi di posta elettronica nell'app di posta elettronica. In iOS, è possibile scorrere rapidamente per eliminare un messaggio::

![](interactivity-images/context-default.png "ListView con azioni di contesto")

Azioni di contesto possono essere implementate in C# e XAML. Di seguito sono elencati le guide di specifiche per entrambi, ma prima è possibile esaminare alcuni dettagli di implementazione della chiave per entrambi.

Azioni di contesto vengono create utilizzando `MenuItem`s. Gli eventi di tocco per MenuItems vengono generati da MenuItem stesso, non il ListView. Ciò è diverso dal modo in cui vengono gestiti gli eventi di tocco per le celle, in cui il ListView genera l'evento piuttosto che la cella. Poiché il ListView sta generando l'evento, il relativo gestore eventi viene specificato con informazioni sulla chiave, ad esempio quale elemento è stato selezionato o toccato.

Per impostazione predefinita, un MenuItem non ha modo di sapere quale cella a cui appartiene. `CommandParameter` sono disponibili sul `MenuItem` per archiviare oggetti, ad esempio l'oggetto sottostante elemento ViewCell di MenuItem. `CommandParameter` può essere impostato in XAML e C#.

### <a name="c"></a>C#  

Azioni di contesto possono essere implementate in una qualsiasi `Cell` sottoclasse (finché non viene usata come un'intestazione di gruppo) creando `MenuItem`s e ad aggiungerle nel `ContextActions` raccolta per la cella. Si hanno le seguenti proprietà possono essere configurate per l'azione rapida:

* **Testo** &ndash; la stringa visualizzata nella voce di menu.
* **Selezionato** &ndash; l'evento quando viene selezionata la voce.
* **IsDestructive** &ndash; (facoltativo) se è true l'elemento viene eseguito il rendering in modo diverso in iOS.

Più azioni di contesto possono essere aggiunti a una cella, ma deve avere solo uno `IsDestructive` impostato su `true`. Il codice seguente illustra come vengono aggiunte alle azioni di contesto un `ViewCell`:

```csharp
var moreAction = new MenuItem { Text = "More" };
moreAction.SetBinding (MenuItem.CommandParameterProperty, new Binding ("."));
moreAction.Clicked += async (sender, e) => {
    var mi = ((MenuItem)sender);
    Debug.WriteLine("More Context Action clicked: " + mi.CommandParameter);
};

var deleteAction = new MenuItem { Text = "Delete", IsDestructive = true }; // red background
deleteAction.SetBinding (MenuItem.CommandParameterProperty, new Binding ("."));
deleteAction.Clicked += async (sender, e) => {
    var mi = ((MenuItem)sender);
    Debug.WriteLine("Delete Context Action clicked: " + mi.CommandParameter);
};
// add to the ViewCell's ContextActions property
ContextActions.Add (moreAction);
ContextActions.Add (deleteAction);
```

### <a name="xaml"></a>XAML

`MenuItem`s può anche essere creato in una raccolta di XAML in modo dichiarativo. il XAML riportato di seguito viene illustrata una cella personalizzata con due azioni di contesto implementate:

```xaml
<ListView x:Name="ContextDemoList">
  <ListView.ItemTemplate>
    <DataTemplate>
      <ViewCell>
         <ViewCell.ContextActions>
            <MenuItem Clicked="OnMore" CommandParameter="{Binding .}"
               Text="More" />
            <MenuItem Clicked="OnDelete" CommandParameter="{Binding .}"
               Text="Delete" IsDestructive="True" />
         </ViewCell.ContextActions>
         <StackLayout Padding="15,0">
              <Label Text="{Binding title}" />
         </StackLayout>
      </ViewCell>
    </DataTemplate>
  </ListView.ItemTemplate>
</ListView>
```

Nel file code-behind, verificare che il `Clicked` vengono implementati i metodi:

```csharp
public void OnMore (object sender, EventArgs e) {
    var mi = ((MenuItem)sender);
    DisplayAlert("More Context Action", mi.CommandParameter + " more context action", "OK");
}

public void OnDelete (object sender, EventArgs e) {
    var mi = ((MenuItem)sender);
    DisplayAlert("Delete Context Action", mi.CommandParameter + " delete context action", "OK");
}
```

> [!NOTE]
> Il `NavigationPageRenderer` per Android include un sottoponibile a override `UpdateMenuItemIcon` metodo che può essere utilizzato per caricare le icone da una classe personalizzata `Drawable`. Questa sostituzione consente di usare le immagini SVG come icone sul `MenuItem` istanze in Android.

<a name="Pull_to_Refresh" />

## <a name="pull-to-refresh"></a>Eseguire il pull per l'aggiornamento

Gli utenti sono ormai si aspettano che scorrere verso il basso in un elenco di dati verrà aggiorna tale elenco. [`ListView`](xref:Xamarin.Forms.ListView) supporta questa out-of-the-box. Per abilitare la funzionalità di pull per aggiornare, impostare [ `IsPullToRefreshEnabled` ](xref:Xamarin.Forms.ListView.IsPullToRefreshEnabled) a `true`:

```xaml
<ListView ...
          IsPullToRefreshEnabled="true" />
```

Il codice C# equivalente è:

```csharp
listView.IsPullToRefreshEnabled = true;
```

Durante l'aggiornamento, ovvero nera per impostazione predefinita viene visualizzata una casella di selezione. Tuttavia, il colore di casella di selezione può essere modificato in iOS e Android impostando il `RefreshControlColor` proprietà per un [ `Color` ](xref:Xamarin.Forms.Color):

```xaml
<ListView ...
          IsPullToRefreshEnabled="true"
          RefreshControlColor="Red" />
```

Il codice C# equivalente è:

```csharp
listView.RefreshControlColor = Color.Red;
```

Le schermate seguenti illustrano pull per aggiornare, come l'utente effettua il pull:

![](interactivity-images/refresh-start.png "ListView Pull per aggiornare In corso")

Le schermate seguenti illustrano pull per aggiornare dopo che l'utente ha rilasciato il pull, con la casella di selezione da visualizzare durante la [ `ListView` ](xref:Xamarin.Forms.ListView) sta aggiornando:

![](interactivity-images/refresh-in-progress.png "Pull di ListView per aggiornamento completato")

[`ListView`](xref:Xamarin.Forms.ListView) viene attivato il [ `Refreshing` ](xref:Xamarin.Forms.ListView.Refreshing) evento per avviare l'aggiornamento e il [ `IsRefreshing` ](xref:Xamarin.Forms.ListView.IsRefreshing) verrà impostata su `true`. È necessario aggiornare il contenuto di qualsiasi riga di codice il `ListView` devono quindi essere eseguiti dal gestore dell'evento per il `Refreshing` evento, o dal metodo eseguito dal [ `RefreshCommand` ](xref:Xamarin.Forms.ListView.RefreshCommand). Una volta il `ListView` viene aggiornato, il `IsRefreshing` deve essere impostata su `false`, o il [ `EndRefresh` ](xref:Xamarin.Forms.ListView.EndRefresh) metodo deve essere chiamato per indicare che l'aggiornamento è stato completato.

> [!NOTE]
> Quando si definisce una [ `RefreshCommand` ](xref:Xamarin.Forms.ListView.RefreshCommand), il `CanExecute` metodo del comando può essere specificato per abilitare o disabilitare il comando.

## <a name="related-links"></a>Collegamenti correlati

- [Interattività ListView (esempio)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/ListView/interactivity)
