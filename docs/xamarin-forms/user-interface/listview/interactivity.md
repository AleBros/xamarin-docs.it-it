---
title: Interattività ListView
description: Questo articolo illustra come aggiungere interattività a un ListView di xamarin. Forms mediante l'implementazione di pull per aggiornare le selezioni e le azioni di contesto.
ms.prod: xamarin
ms.assetid: CD14EB90-B08C-4E8F-A314-DA0EEC76E647
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/25/2019
ms.openlocfilehash: aa717792bdaefe24d957c9781934933b67aaf92b
ms.sourcegitcommit: eca3b01098dba004d367292c8b0d74b58c4e1206
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79305332"
---
# <a name="listview-interactivity"></a>Interattività ListView

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-listview-interactivity)

La classe [`ListView`](xref:Xamarin.Forms.ListView) Novell. Forms supporta l'interazione dell'utente con i dati che presenta.

## <a name="selection-and-taps"></a>Selezione e tocchi

La modalità di selezione [`ListView`](xref:Xamarin.Forms.ListView) viene controllata impostando la proprietà [`ListView.SelectionMode`](xref:Xamarin.Forms.ListView.SelectionMode) su un valore dell'enumerazione [`ListViewSelectionMode`](xref:Xamarin.Forms.ListViewSelectionMode) :

- [`Single`](xref:Xamarin.Forms.ListViewSelectionMode.Single) indica che è possibile selezionare un singolo elemento, con l'elemento selezionato evidenziato. Si tratta del valore predefinito.
- [`None`](xref:Xamarin.Forms.ListViewSelectionMode.None) indica che non è possibile selezionare gli elementi.

Quando un utente tocca un elemento, vengono generati due eventi:

- [`ItemSelected`](xref:Xamarin.Forms.ListView.ItemSelected) viene attivato quando viene selezionato un nuovo elemento.
- [`ItemTapped`](xref:Xamarin.Forms.ListView.ItemTapped) viene attivato quando viene toccato un elemento.

Se si tocca lo stesso elemento due volte, viene generato un solo evento di [`ItemSelected`](xref:Xamarin.Forms.ListView.ItemSelected) [`ItemTapped`](xref:Xamarin.Forms.ListView.ItemTapped) .

> [!NOTE]
> La classe [`ItemTappedEventArgs`](xref:Xamarin.Forms.ItemTappedEventArgs) , che contiene gli argomenti degli eventi per l'evento [`ItemTapped`](xref:Xamarin.Forms.ListView.ItemTapped) , ha proprietà [`Group`](xref:Xamarin.Forms.ItemTappedEventArgs.Group) e [`Item`](xref:Xamarin.Forms.ItemTappedEventArgs.Item) e una proprietà `ItemIndex` il cui valore rappresenta l'indice nel [`ListView`](xref:Xamarin.Forms.ListView) dell'elemento toccato. Analogamente, la classe [`SelectedItemChangedEventArgs`](xref:Xamarin.Forms.SelectedItemChangedEventArgs) , che contiene gli argomenti dell'evento per l'evento [`ItemSelected`](xref:Xamarin.Forms.ListView.ItemSelected) , ha una proprietà [`SelectedItem`](xref:Xamarin.Forms.SelectedItemChangedEventArgs.SelectedItem) e una proprietà `SelectedItemIndex` il cui valore rappresenta l'indice nel `ListView` dell'elemento selezionato.

Quando la proprietà [`SelectionMode`](xref:Xamarin.Forms.ListView.SelectionMode) è impostata su [`Single`](xref:Xamarin.Forms.ListViewSelectionMode.Single), è possibile selezionare gli elementi del [`ListView`](xref:Xamarin.Forms.ListView) , verranno generati gli eventi [`ItemSelected`](xref:Xamarin.Forms.ListView.ItemSelected) e [`ItemTapped`](xref:Xamarin.Forms.ListView.ItemTapped) e la proprietà [`SelectedItem`](xref:Xamarin.Forms.ListView.SelectedItem) verrà impostata sul valore dell'elemento selezionato.

Quando la proprietà [`SelectionMode`](xref:Xamarin.Forms.ListView.SelectionMode) è impostata su [`None`](xref:Xamarin.Forms.ListViewSelectionMode.None), non è possibile selezionare gli elementi del [`ListView`](xref:Xamarin.Forms.ListView) , l'evento [`ItemSelected`](xref:Xamarin.Forms.ListView.ItemSelected) non verrà generato e la proprietà [`SelectedItem`](xref:Xamarin.Forms.ListView.SelectedItem) rimarrà `null`. Tuttavia, gli eventi [`ItemTapped`](xref:Xamarin.Forms.ListView.ItemTapped) verranno comunque attivati e l'elemento toccato verrà brevemente evidenziato durante il tocco.

Quando viene selezionato un elemento e la proprietà [`SelectionMode`](xref:Xamarin.Forms.ListView.SelectionMode) viene modificata da [`Single`](xref:Xamarin.Forms.ListViewSelectionMode.Single) a [`None`](xref:Xamarin.Forms.ListViewSelectionMode.None), la proprietà [`SelectedItem`](xref:Xamarin.Forms.ListView.SelectedItem) verrà impostata su `null` e l'evento [`ItemSelected`](xref:Xamarin.Forms.ListView.ItemSelected) verrà generato con un elemento `null`.

Gli screenshot seguenti mostrano un [`ListView`](xref:Xamarin.Forms.ListView) con la modalità di selezione predefinita:

![](interactivity-images/selection-default.png "ListView with Selection Enabled")

### <a name="disable-selection"></a>Disabilita selezione

Per disabilitare [`ListView`](xref:Xamarin.Forms.ListView) selezione, impostare la proprietà [`SelectionMode`](xref:Xamarin.Forms.ListView.SelectionMode) su [`None`](xref:Xamarin.Forms.ListViewSelectionMode.None):

```xaml
<ListView ... SelectionMode="None" />
```

```csharp
var listView = new ListView { ... SelectionMode = ListViewSelectionMode.None };
```

## <a name="context-actions"></a>Azioni di contesto

Spesso gli utenti desiderano eseguire un'azione su un elemento in un `ListView`. Si consideri ad esempio un elenco di messaggi di posta elettronica nell'app di posta elettronica. In iOS è possibile scorrere rapidamente per eliminare un messaggio:

![](interactivity-images/context-default.png "ListView with Context Actions")

Azioni di contesto possono essere implementate in c# e XAML. Di seguito sono elencati le guide di specifiche per entrambi, ma prima è possibile esaminare alcuni dettagli di implementazione della chiave per entrambi.

Le azioni di contesto vengono create usando elementi `MenuItem`. Gli eventi tap per `MenuItems` gli oggetti vengono generati dal `MenuItem` stesso, non dal `ListView`. Questo è diverso da quello in cui vengono gestiti gli eventi tap per le celle, in cui il `ListView` genera l'evento anziché la cella. Poiché il `ListView` genera l'evento, al relativo gestore eventi vengono fornite informazioni chiave, ad esempio l'elemento selezionato o toccato.

Per impostazione predefinita, un `MenuItem` non ha alcun modo per conoscere la cella a cui appartiene. La proprietà `CommandParameter` è disponibile in `MenuItem` per archiviare oggetti, ad esempio l'oggetto dietro il `ViewCell`di `MenuItem`. È possibile impostare la proprietà `CommandParameter` sia in XAML sia C#in.

### <a name="xaml"></a>XAML

gli elementi `MenuItem` possono essere creati in una raccolta XAML. il XAML riportato di seguito viene illustrata una cella personalizzata con due azioni di contesto implementate:

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

Nel file code-behind verificare che siano implementati i metodi `Clicked`:

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
> Il `NavigationPageRenderer` per Android dispone di un metodo di `UpdateMenuItemIcon` sottoponibile a override che può essere usato per caricare icone da un `Drawable`personalizzato. Questa sostituzione consente di usare immagini SVG come icone in `MenuItem` istanze in Android.

### <a name="code"></a>Codice

Le azioni di contesto possono essere implementate in qualsiasi sottoclasse `Cell` (purché non venga usata come intestazione di gruppo) creando `MenuItem` istanze e aggiungendole alla raccolta di `ContextActions` per la cella. Si hanno le seguenti proprietà possono essere configurate per l'azione rapida:

- **Testo** &ndash; stringa visualizzata nella voce di menu.
- **Selezionato** &ndash; evento quando si fa clic sull'elemento.
- &ndash; **distruttive** (facoltativo) quando il valore è true, il rendering dell'elemento viene eseguito in modo diverso in iOS.

È possibile aggiungere più azioni di contesto a una cella, ma solo una deve avere `IsDestructive` impostata su `true`. Nel codice seguente viene illustrata la modalità di aggiunta delle azioni di contesto a un `ViewCell`:

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

## <a name="pull-to-refresh"></a>Pull per aggiornare

Gli utenti sono ormai si aspettano che scorrere verso il basso in un elenco di dati verrà aggiorna tale elenco. Il controllo [`ListView`](xref:Xamarin.Forms.ListView) supporta questa operazione predefinita. Per abilitare la funzionalità pull-to-refresh, impostare [`IsPullToRefreshEnabled`](xref:Xamarin.Forms.ListView.IsPullToRefreshEnabled) su `true`:

```xaml
<ListView ...
          IsPullToRefreshEnabled="true" />
```

Il codice C# equivalente è il seguente:

```csharp
listView.IsPullToRefreshEnabled = true;
```

Viene visualizzata una casella di selezione durante l'aggiornamento, che per impostazione predefinita è nero. Tuttavia, il colore della casella di selezione può essere modificato in iOS e Android impostando la proprietà `RefreshControlColor` su un [`Color`](xref:Xamarin.Forms.Color):

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

Gli screenshot seguenti mostrano l'operazione pull-to-refresh dopo che l'utente ha rilasciato il pull, con la casella di selezione visualizzata durante l'aggiornamento del [`ListView`](xref:Xamarin.Forms.ListView) :

![](interactivity-images/refresh-in-progress.png "ListView Pull to Refresh Complete")

[`ListView`](xref:Xamarin.Forms.ListView) genera l'evento [`Refreshing`](xref:Xamarin.Forms.ListView.Refreshing) per avviare l'aggiornamento e la proprietà [`IsRefreshing`](xref:Xamarin.Forms.ListView.IsRefreshing) verrà impostata su `true`. Qualsiasi codice necessario per aggiornare il contenuto del `ListView` deve quindi essere eseguito dal gestore eventi per l'evento `Refreshing` o dal metodo eseguito dal [`RefreshCommand`](xref:Xamarin.Forms.ListView.RefreshCommand). Una volta aggiornato il `ListView`, è necessario impostare la proprietà `IsRefreshing` su `false`oppure chiamare il metodo [`EndRefresh`](xref:Xamarin.Forms.ListView.EndRefresh) per indicare che l'aggiornamento è stato completato.

> [!NOTE]
> Quando si definisce una [`RefreshCommand`](xref:Xamarin.Forms.ListView.RefreshCommand), è possibile specificare il `CanExecute` metodo del comando per abilitare o disabilitare il comando.

## <a name="detect-scrolling"></a>Rileva scorrimento

[`ListView`](xref:Xamarin.Forms.ListView) definisce un evento `Scrolled` generato per indicare che si è verificato lo scorrimento. Nell'esempio di codice XAML riportato di seguito viene illustrato un `ListView` che imposta un gestore eventi per l'evento `Scrolled`:

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

In questo esempio di codice, il gestore dell'evento `OnListViewScrolled` viene eseguito quando viene generato l'evento `Scrolled`:

```csharp
void OnListViewScrolled(object sender, ScrolledEventArgs e)
{
    Debug.WriteLine("ScrollX: " + e.ScrollX);
    Debug.WriteLine("ScrollY: " + e.ScrollY);  
}
```

Il gestore dell'evento `OnListViewScrolled` restituisce i valori dell'oggetto `ScrolledEventArgs` che accompagna l'evento.

## <a name="related-links"></a>Collegamenti correlati

- [Interattività ListView (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-listview-interactivity)
