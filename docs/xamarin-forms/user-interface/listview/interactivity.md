---
title: "ListView Interactivity" Description: "in questo articolo viene illustrato come aggiungere interattività a un Xamarin.Forms ListView implementando selezioni, azioni di contesto e pull-to-refresh".
ms. prod: Novell MS. AssetID: CD14EB90-B08C-4E8F-A314-DA0EEC76E647 ms. Technology: Novell-Forms Author: davidbritch ms. Author: dabritch ms. Date: 09/25/2019 no-loc: [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="listview-interactivity"></a>Interattività del controllo ListView

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-listview-interactivity)

La Xamarin.Forms [`ListView`](xref:Xamarin.Forms.ListView) classe supporta l'interazione dell'utente con i dati che presenta.

## <a name="selection-and-taps"></a>Selezione e tocchi

La [`ListView`](xref:Xamarin.Forms.ListView) modalità di selezione viene controllata impostando la [`ListView.SelectionMode`](xref:Xamarin.Forms.ListView.SelectionMode) proprietà su un valore dell' [`ListViewSelectionMode`](xref:Xamarin.Forms.ListViewSelectionMode) enumerazione:

- [`Single`](xref:Xamarin.Forms.ListViewSelectionMode.Single)indica che è possibile selezionare un singolo elemento, con l'elemento selezionato evidenziato. Si tratta del valore predefinito.
- [`None`](xref:Xamarin.Forms.ListViewSelectionMode.None)indica che non è possibile selezionare gli elementi.

Quando un utente tocca un elemento, vengono generati due eventi:

- [`ItemSelected`](xref:Xamarin.Forms.ListView.ItemSelected)generato quando viene selezionato un nuovo elemento.
- [`ItemTapped`](xref:Xamarin.Forms.ListView.ItemTapped)generato quando viene toccato un elemento.

Se si tocca lo stesso elemento due volte, viene generato [`ItemTapped`](xref:Xamarin.Forms.ListView.ItemTapped) un solo [`ItemSelected`](xref:Xamarin.Forms.ListView.ItemSelected) evento.

> [!NOTE]
> La [`ItemTappedEventArgs`](xref:Xamarin.Forms.ItemTappedEventArgs) classe, che contiene gli argomenti dell'evento per l' [`ItemTapped`](xref:Xamarin.Forms.ListView.ItemTapped) evento, ha le [`Group`](xref:Xamarin.Forms.ItemTappedEventArgs.Group) [`Item`](xref:Xamarin.Forms.ItemTappedEventArgs.Item) proprietà e e una `ItemIndex` proprietà il cui valore rappresenta l'indice nell' [`ListView`](xref:Xamarin.Forms.ListView) oggetto dell'elemento toccata. Analogamente, la [`SelectedItemChangedEventArgs`](xref:Xamarin.Forms.SelectedItemChangedEventArgs) classe, che contiene gli argomenti dell'evento per l' [`ItemSelected`](xref:Xamarin.Forms.ListView.ItemSelected) evento, ha una [`SelectedItem`](xref:Xamarin.Forms.SelectedItemChangedEventArgs.SelectedItem) proprietà e una `SelectedItemIndex` proprietà il cui valore rappresenta l'indice nell' `ListView` oggetto dell'elemento selezionato.

Quando la [`SelectionMode`](xref:Xamarin.Forms.ListView.SelectionMode) proprietà è impostata su [`Single`](xref:Xamarin.Forms.ListViewSelectionMode.Single) , gli elementi in [`ListView`](xref:Xamarin.Forms.ListView) possono essere selezionati, gli [`ItemSelected`](xref:Xamarin.Forms.ListView.ItemSelected) [`ItemTapped`](xref:Xamarin.Forms.ListView.ItemTapped) eventi e verranno generati e la [`SelectedItem`](xref:Xamarin.Forms.ListView.SelectedItem) proprietà verrà impostata sul valore dell'elemento selezionato.

Quando la [`SelectionMode`](xref:Xamarin.Forms.ListView.SelectionMode) proprietà è impostata su [`None`](xref:Xamarin.Forms.ListViewSelectionMode.None) , gli elementi in [`ListView`](xref:Xamarin.Forms.ListView) non possono essere selezionati, l' [`ItemSelected`](xref:Xamarin.Forms.ListView.ItemSelected) evento non verrà generato e la [`SelectedItem`](xref:Xamarin.Forms.ListView.SelectedItem) proprietà `null` rimarrà. Tuttavia, [`ItemTapped`](xref:Xamarin.Forms.ListView.ItemTapped) gli eventi verranno comunque generati e l'elemento toccato verrà evidenziato brevemente durante il tocco.

Quando viene selezionato un elemento e la [`SelectionMode`](xref:Xamarin.Forms.ListView.SelectionMode) proprietà viene modificata da [`Single`](xref:Xamarin.Forms.ListViewSelectionMode.Single) a [`None`](xref:Xamarin.Forms.ListViewSelectionMode.None) , la proprietà viene [`SelectedItem`](xref:Xamarin.Forms.ListView.SelectedItem) impostata su `null` e l' [`ItemSelected`](xref:Xamarin.Forms.ListView.ItemSelected) evento viene generato con un `null` elemento.

Gli screenshot seguenti mostrano un [`ListView`](xref:Xamarin.Forms.ListView) con la modalità di selezione predefinita:

![](interactivity-images/selection-default.png "ListView with Selection Enabled")

### <a name="disable-selection"></a>Disabilita selezione

Per disabilitare [`ListView`](xref:Xamarin.Forms.ListView) la selezione, impostare la [`SelectionMode`](xref:Xamarin.Forms.ListView.SelectionMode) proprietà su [`None`](xref:Xamarin.Forms.ListViewSelectionMode.None) :

```xaml
<ListView ... SelectionMode="None" />
```

```csharp
var listView = new ListView { ... SelectionMode = ListViewSelectionMode.None };
```

## <a name="context-actions"></a>Azioni di contesto

Spesso gli utenti vorranno eseguire un'azione su un elemento in un `ListView` . Si consideri, ad esempio, un elenco di messaggi di posta elettronica nell'app di posta elettronica. In iOS è possibile scorrere rapidamente per eliminare un messaggio:

![](interactivity-images/context-default.png "ListView with Context Actions")

Le azioni di contesto possono essere implementate in C# e XAML. Di seguito sono riportate alcune guide specifiche per entrambi, ma per prima cosa verranno esaminati alcuni dettagli di implementazione chiave per entrambi.

Le azioni di contesto vengono create usando `MenuItem` gli elementi. Gli eventi tap per `MenuItems` gli oggetti vengono generati da `MenuItem` se stessi, non da `ListView` . Questo è diverso da quello in cui vengono gestiti gli eventi tap per le celle, in cui `ListView` genera l'evento anziché la cella. Poiché `ListView` genera l'evento, al gestore eventi vengono fornite informazioni chiave, ad esempio l'elemento selezionato o toccato.

Per impostazione predefinita, un oggetto `MenuItem` non ha alcun modo per sapere a quale cella appartiene. La `CommandParameter` proprietà è disponibile in `MenuItem` per archiviare oggetti, ad esempio l'oggetto dietro a `MenuItem` `ViewCell` . La `CommandParameter` proprietà può essere impostata sia in XAML che in C#.

### <a name="xaml"></a>XAML

`MenuItem`gli elementi possono essere creati in una raccolta XAML. Il codice XAML seguente illustra una cella personalizzata con due azioni del contesto implementate:

```xaml
<ListView x:Name="ContextDemoList">
  <ListView.ItemTemplate>
    <DataTemplate>
      <ViewCell>
         <ViewCell.ContextActions>
            <MenuItem Clicked="OnMore"
                      CommandParameter="{Binding .}"
                      Text="More" />
            <MenuItem Clicked="OnDelete"
                      CommandParameter="{Binding .}"
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

Nel file code-behind verificare che i `Clicked` metodi siano implementati:

```csharp
public void OnMore (object sender, EventArgs e)
{
    var mi = ((MenuItem)sender);
    DisplayAlert("More Context Action", mi.CommandParameter + " more context action", "OK");
}

public void OnDelete (object sender, EventArgs e)
{
    var mi = ((MenuItem)sender);
    DisplayAlert("Delete Context Action", mi.CommandParameter + " delete context action", "OK");
}
```

> [!NOTE]
> `NavigationPageRenderer`Per Android è presente un metodo sottoponibile `UpdateMenuItemIcon` a override che può essere usato per caricare icone da un oggetto personalizzato `Drawable` . Questo override rende possibile l'uso di immagini SVG come icone nelle `MenuItem` istanze di in Android.

### <a name="code"></a>Codice

Le azioni di contesto possono essere implementate in qualsiasi `Cell` sottoclasse (purché non venga usata come intestazione di gruppo) creando `MenuItem` istanze e aggiungendole alla `ContextActions` raccolta per la cella. Per l'azione di contesto è possibile configurare le proprietà seguenti:

- **Testo** &ndash; stringa visualizzata nella voce di menu.
- **Selezionato** &ndash; evento quando si fa clic sull'elemento.
- **Distruttive** &ndash; (facoltativo) quando il valore è true, il rendering dell'elemento viene eseguito in modo diverso in iOS.

È possibile aggiungere più azioni di contesto a una cella, ma solo una deve avere `IsDestructive` impostato su `true` . Nel codice seguente viene illustrata la modalità di aggiunta delle azioni di contesto a un oggetto `ViewCell` :

```csharp
var moreAction = new MenuItem { Text = "More" };
moreAction.SetBinding (MenuItem.CommandParameterProperty, new Binding ("."));
moreAction.Clicked += async (sender, e) =>
{
    var mi = ((MenuItem)sender);
    Debug.WriteLine("More Context Action clicked: " + mi.CommandParameter);
};

var deleteAction = new MenuItem { Text = "Delete", IsDestructive = true }; // red background
deleteAction.SetBinding (MenuItem.CommandParameterProperty, new Binding ("."));
deleteAction.Clicked += async (sender, e) =>
{
    var mi = ((MenuItem)sender);
    Debug.WriteLine("Delete Context Action clicked: " + mi.CommandParameter);
};
// add to the ViewCell's ContextActions property
ContextActions.Add (moreAction);
ContextActions.Add (deleteAction);
```

## <a name="pull-to-refresh"></a>Aggiornamento tramite trascinamento verso il basso

Gli utenti si aspettano che l'elenco venga aggiornato in un elenco di dati. Il [`ListView`](xref:Xamarin.Forms.ListView) controllo supporta questa operazione predefinita. Per abilitare la funzionalità pull-to-refresh, impostare [`IsPullToRefreshEnabled`](xref:Xamarin.Forms.ListView.IsPullToRefreshEnabled) su `true` :

```xaml
<ListView ...
          IsPullToRefreshEnabled="true" />
```

Il codice C# equivalente è il seguente:

```csharp
listView.IsPullToRefreshEnabled = true;
```

Viene visualizzata una casella di selezione durante l'aggiornamento, che per impostazione predefinita è nero. Tuttavia, il colore della casella di selezione può essere modificato in iOS e Android impostando la `RefreshControlColor` proprietà su [`Color`](xref:Xamarin.Forms.Color) :

```xaml
<ListView ...
          IsPullToRefreshEnabled="true"
          RefreshControlColor="Red" />
```

Il codice C# equivalente è il seguente:

```csharp
listView.RefreshControlColor = Color.Red;
```

Gli screenshot seguenti mostrano il pull per l'aggiornamento quando l'utente esegue il pull:

![](interactivity-images/refresh-start.png "ListView Pull to Refresh In-Progress")

Gli screenshot seguenti mostrano l'operazione pull-to-refresh dopo che l'utente ha rilasciato il pull, con la casella di selezione visualizzata durante l' [`ListView`](xref:Xamarin.Forms.ListView) aggiornamento di:

![](interactivity-images/refresh-in-progress.png "ListView Pull to Refresh Complete")

[`ListView`](xref:Xamarin.Forms.ListView)genera l' [`Refreshing`](xref:Xamarin.Forms.ListView.Refreshing) evento per avviare l'aggiornamento e la [`IsRefreshing`](xref:Xamarin.Forms.ListView.IsRefreshing) proprietà verrà impostata su `true` . Qualsiasi codice necessario per aggiornare il contenuto di `ListView` deve quindi essere eseguito dal gestore eventi per l' `Refreshing` evento o dal metodo eseguito da [`RefreshCommand`](xref:Xamarin.Forms.ListView.RefreshCommand) . Una volta `ListView` aggiornato l'oggetto, la `IsRefreshing` proprietà deve essere impostata su `false` oppure [`EndRefresh`](xref:Xamarin.Forms.ListView.EndRefresh) è necessario chiamare il metodo per indicare che l'aggiornamento è stato completato.

> [!NOTE]
> Quando si definisce un oggetto [`RefreshCommand`](xref:Xamarin.Forms.ListView.RefreshCommand) , `CanExecute` è possibile specificare il metodo del comando per abilitare o disabilitare il comando.

## <a name="detect-scrolling"></a>Rileva scorrimento

[`ListView`](xref:Xamarin.Forms.ListView)definisce un `Scrolled` evento generato per indicare che si è verificato lo scorrimento. Nell'esempio di codice XAML riportato di seguito viene illustrato un oggetto `ListView` che imposta un gestore eventi per l' `Scrolled` evento:

```xaml
<ListView Scrolled="OnListViewScrolled">
    ...
</ListView>
```

Il codice C# equivalente è il seguente:

```csharp
ListView listView = new ListView();
listView.Scrolled += OnListViewScrolled;
```

In questo esempio di codice, il `OnListViewScrolled` gestore eventi viene eseguito quando viene `Scrolled` generato l'evento:

```csharp
void OnListViewScrolled(object sender, ScrolledEventArgs e)
{
    Debug.WriteLine("ScrollX: " + e.ScrollX);
    Debug.WriteLine("ScrollY: " + e.ScrollY);  
}
```

Il `OnListViewScrolled` gestore eventi restituisce i valori dell' `ScrolledEventArgs` oggetto che accompagna l'evento.

## <a name="related-links"></a>Collegamenti correlati

- [Interattività ListView (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-listview-interactivity)
