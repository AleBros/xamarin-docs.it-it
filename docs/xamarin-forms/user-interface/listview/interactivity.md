---
title: Interattività di ListView
description: Aggiungere interattività al ListView mediante l'implementazione delle selezioni, scorrere rapidamente per eliminare e pull per l'aggiornamento.
ms.prod: xamarin
ms.assetid: CD14EB90-B08C-4E8F-A314-DA0EEC76E647
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/01/2018
ms.openlocfilehash: 5fe821e7e5254da8febbbde518b9fd42526bf262
ms.sourcegitcommit: d80d93957040a14b4638a91b0eac797cfaade840
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/07/2018
ms.locfileid: "34848122"
---
# <a name="listview-interactivity"></a>Interattività di ListView

ListView supporta l'interazione con i dati che presenta uno dei seguenti modi:

- [**Selezione & scelte** ](#selectiontaps) &ndash; rispondere alle scelte e le selezioni/deselezioni non di elementi. Abilitare o disabilitare la selezione della riga (abilitato per impostazione predefinita).
- [**Azioni del contesto** ](#Context_Actions) &ndash; espongono funzionalità per ogni elemento, ad esempio, scorrere rapidamente per eliminare.
- [**Pull per l'aggiornamento** ](#Pull_to_Refresh) &ndash; implementare il linguaggio di pull per l'aggiornamento che gli utenti aspettano da esperienze native.

<a name="selectiontaps" />

## <a name="selection--taps"></a>Selezione & scelte
`ListView` supporta la selezione di un elemento alla volta. È selezionata per impostazione predefinita. Quando un utente tocca un elemento, vengono generati due eventi: `ItemTapped` e `ItemSelected`. Si noti che se si tocca due volte lo stesso elemento non funzionerà più `ItemSelected` eventi, ma genererà più `ItemTapped` eventi. Si noti inoltre che `ItemSelected` verrà chiamato se un elemento è deselezionato.

Tenere presente che `ItemSelected` viene chiamato quando si sono deselezionati e quando vengono selezionati. Pertanto, è necessario controllare i valori null `SelectedItem` nel `ItemSelected` gestore dell'evento prima di poter utilizzare:

```csharp
void OnSelection (object sender, SelectedItemChangedEventArgs e)
{
  if (e.SelectedItem == null) {
    return; //ItemSelected is called on deselection, which results in SelectedItem being set to null
  }
  DisplayAlert ("Item Selected", e.SelectedItem.ToString (), "Ok");
  //((ListView)sender).SelectedItem = null; //uncomment line if you want to disable the visual selection state.
}
```

### <a name="disabling-selection"></a>La disabilitazione di selezione

Se si desidera disabilitare la selezione, gestire il `ItemSelected` evento e impostare il `SelectedItem` proprietà su null:

```csharp
SelectionDemoList.ItemSelected += (sender, e) => {
    ((ListView)sender).SelectedItem = null;
};
```

Con la selezione abilitato:

![](interactivity-images/selection-default.png "ListView con selezione abilitato")

<a name="Context_Actions" />

## <a name="context-actions"></a>Azioni del contesto
Spesso, gli utenti desiderano esegue azioni su un elemento in un `ListView`. Si consideri ad esempio un elenco di messaggi di posta elettronica in app della posta elettronica. In iOS, è possibile scorrere rapidamente per eliminare un messaggio:

![](interactivity-images/context-default.png "ListView con azioni di contesto")

Azioni del contesto possono essere implementate in c# e XAML. Di seguito sono disponibili guide specifiche per entrambi, ma prima di tutto si dare un'occhiata alcuni dettagli di implementazione della chiave per entrambi.

Azioni del contesto vengono create utilizzando `MenuItem`s. Gli eventi tocco MenuItems generati da MenuItem stesso, non il controllo ListView. Questa è diversa dalla modalità in cui vengono gestiti gli eventi di tocco per le celle, in cui il controllo ListView genera l'evento anziché la cella. Poiché ListView genera l'evento, il gestore dell'evento è specificato con informazioni sulla chiave, ad esempio quale elemento selezionato o scelte.

Per impostazione predefinita, un MenuItem non ha modo di conoscere la cella a cui appartiene. `CommandParameter` è disponibile in `MenuItem` per archiviare oggetti, ad esempio l'oggetto sottostante ViewCell di MenuItem. `CommandParameter` può essere impostato in XAML e c#.

### <a name="c"></a>C#  

Azioni del contesto possono essere implementate in qualsiasi `Cell` sottoclasse (purché non sia in uso come un'intestazione di gruppo) creando `MenuItem`s e aggiungendoli al `ContextActions` insieme per la cella. Sono disponibili i seguenti proprietà possono essere configurate per l'azione di contesto:

* **Testo** &ndash; la stringa visualizzata nella voce di menu.
* **Selezionato** &ndash; l'evento quando viene selezionata la voce.
* **IsDestructive** &ndash; (facoltativo) se è true l'elemento viene eseguito il rendering in modo diverso in iOS.

Più azioni di contesto possono essere aggiunti a una cella, ma devono essere presenti solo `IsDestructive` impostato su `true`. Il codice seguente illustra come azioni del contesto verrebbero aggiunta a un `ViewCell`:

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

`MenuItem`s possono anche essere creati in una raccolta di XAML in modo dichiarativo. Il codice XAML seguente viene illustrata una cella personalizzata con due azioni contesto implementate:

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

Nel file code-behind, assicurarsi di `Clicked` vengono implementati i metodi:

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
> Il `NavigationPageRenderer` per Android ha un sottoponibile a override `UpdateMenuItemIcon` metodo che può essere utilizzato per caricare le icone da un oggetto personalizzato `Drawable`. Questa sostituzione consente di utilizzare le immagini SVG come icone sul `MenuItem` istanze in Android.

<a name="Pull_to_Refresh" />

## <a name="pull-to-refresh"></a>Effettuare il pull di aggiornamento
Gli utenti aspettano pull verso il basso in un elenco di dati verrà aggiornato l'elenco. `ListView` supporta questa out-of-the-box. Per abilitare la funzionalità di scorrimento per l'aggiornamento, impostare `IsPullToRefreshEnabled` su true:

```csharp
listView.IsPullToRefreshEnabled = true;
```

Effettua il pull di pull per l'aggiornamento dell'utente:

![](interactivity-images/refresh-start.png "ListView Pull per aggiornare In corso")

Pull per l'aggiornamento come l'utente ha rilasciato il pull. Questo è ciò che l'utente vede mentre si sta aggiornando elenco: ![ ] (interactivity-images/refresh-in-progress.png "ListView Pull di aggiornamento completo")

ListView espone alcuni eventi che consentono di rispondere agli eventi di scorrimento per l'aggiornamento.

-  Il `RefreshCommand` verrà richiamato e `Refreshing` evento denominato. `IsRefreshing` verrà impostato su `true`.
-  È consigliabile eseguire qualsiasi codice è necessario per aggiornare il contenuto della visualizzazione elenco, nel comando o evento.
-  Durante l'aggiornamento è completato, chiamare `EndRefresh` o impostare `IsRefreshing` a `false` per indicare la visualizzazione elenco che è terminato.

Il `CanExecute` proprietà viene rispettata, che offre un modo per controllare se il comando di pull per l'aggiornamento deve essere attivato.



## <a name="related-links"></a>Collegamenti correlati

- [ListView interattività (esempio)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/ListView/interactivity)
- [note sulla versione 1.4](http://forums.xamarin.com/discussion/35451/xamarin-forms-1-4-0-released/)
- [note sulla versione 1.3](http://forums.xamarin.com/discussion/29934/xamarin-forms-1-3-0-released/)
