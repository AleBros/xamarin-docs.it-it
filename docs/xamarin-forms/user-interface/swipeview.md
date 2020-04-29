---
title: Novell. Forms SwipeView
description: Novell. Forms SwipeView è un controllo contenitore che esegue il wrapping di un elemento di contenuto e fornisce voci di menu di scelta rapida che vengono rivelate da un gesto di scorrimento.
ms.prod: xamarin
ms.assetId: 602456B5-701B-4948-B454-B1F31283F1CF
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/26/2020
ms.openlocfilehash: da6dbe63b7151ef0f9a1defca66fbb3abb25ad1d
ms.sourcegitcommit: 8d13d2262d02468c99c4e18207d50cd82275d233
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/29/2020
ms.locfileid: "82517544"
---
# <a name="xamarinforms-swipeview"></a>Novell. Forms SwipeView

![](~/media/shared/preview.png "This API is currently pre-release")

[![Scaricare l'](~/media/shared/download.png) esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-swipeviewdemos/)

È `SwipeView` un controllo contenitore che esegue il wrapping di un elemento di contenuto e fornisce voci di menu di scelta rapida rivelate da un gesto di scorrimento:

[![Screenshot di SwipeView swipe items in a CollectionView, in iOS e Android](swipeview-images/swipeview-collectionview.png "SwipeView swipe elementi")](swipeview-images/swipeview-collectionview-large.png#lightbox "SwipeView swipe elementi")

`SwipeView`è disponibile in Novell. Forms 4,4. Tuttavia, `AppDelegate` è attualmente sperimentale e può essere usata solo aggiungendo la seguente riga di codice alla classe in iOS, alla `MainActivity` classe in Android o alla `App` classe in UWP, prima di chiamare: `Forms.Init`

```csharp
Forms.SetFlags("SwipeView_Experimental");
```

`SwipeView` definisce le proprietà seguenti:

- `LeftItems`, di tipo `SwipeItems`, che rappresenta gli elementi swipe che possono essere richiamati quando il controllo viene sottoposto a scorrimento dal lato sinistro.
- `RightItems`, di tipo `SwipeItems`, che rappresenta gli elementi swipe che possono essere richiamati quando il controllo viene sottoposto a scorrimento dal lato destro.
- `TopItems`, di tipo `SwipeItems`, che rappresenta gli elementi swipe che possono essere richiamati quando il controllo viene sottoposto a scorrimento dall'alto verso il basso.
- `BottomItems`, di tipo `SwipeItems`, che rappresenta gli elementi swipe che possono essere richiamati quando il controllo viene sottoposto a scorrimento dal basso verso l'alto.

Queste proprietà sono supportate da [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) oggetti, il che significa che possono essere destinazioni di data binding e con stile.

`SwipeView` Eredita inoltre la [`Content`](xref:Xamarin.Forms.ContentView.Content) proprietà dalla [`ContentView`](xref:Xamarin.Forms.ContentView) classe. La `Content` proprietà è la proprietà di contenuto della `SwipeView` classe e pertanto non deve essere impostata in modo esplicito.

La `SwipeView` classe definisce anche quattro eventi:

- `SwipeStarted`viene generato all'avvio di un scorrimento rapido. L' `SwipeStartedEventArgs` oggetto che accompagna questo evento ha una `SwipeDirection` proprietà di tipo. `SwipeDirection`
- `SwipeChanging`viene generato quando si sposta il dito. L' `SwipeChangingEventArgs` oggetto che accompagna questo evento ha `SwipeDirection` una proprietà, di tipo `SwipeDirection`e una `Offset` proprietà di tipo. `double`
- `SwipeEnded`viene generato al termine di un swipe. L' `SwipeEndedEventArgs` oggetto che accompagna questo evento ha una `SwipeDirection` proprietà di tipo. `SwipeDirection`
- `CloseRequested`viene generato quando gli elementi di scorrimento vengono chiusi.

`SwipeView` Include `Open` inoltre i metodi e `Close` , che aprono e chiudono a livello di codice gli elementi di scorrimento, rispettivamente.

> [!NOTE]
> `SwipeView`dispone di una piattaforma specifica in iOS e Android, che controlla la transizione utilizzata durante l'apertura di un `SwipeView`oggetto. Per altre informazioni, vedere [modalità di transizione SwipeView swipe in iOS](~/xamarin-forms/platform/ios/swipeview-swipetransitionmode.md) e la [modalità di transizione SwipeView swipe in Android](~/xamarin-forms/platform/android/swipeview-swipetransitionmode.md).

## <a name="create-a-swipeview"></a>Creare un SwipeView

Un `SwipeView` oggetto deve definire il contenuto che `SwipeView` esegue il wrapping e gli elementi di scorrimento che vengono rivelati dal gesto di scorrimento. Gli elementi swipe sono uno o più `SwipeItem` oggetti `SwipeView` posizionati in una delle quattro raccolte direzionali `LeftItems`, ovvero, `RightItems` `TopItems`, o. `BottomItems`

Nell'esempio seguente viene illustrato come creare un'istanza `SwipeView` di in XAML:

```xaml
<SwipeView>
    <SwipeView.LeftItems>
        <SwipeItems>
            <SwipeItem Text="Favorite"
                       IconImageSource="favorite.png"
                       BackgroundColor="LightGreen"
                       Invoked="OnFavoriteSwipeItemInvoked" />
            <SwipeItem Text="Delete"
                       IconImageSource="delete.png"
                       BackgroundColor="LightPink"
                       Invoked="OnDeleteSwipeItemInvoked" />
        </SwipeItems>
    </SwipeView.LeftItems>
    <!-- Content -->
    <Grid HeightRequest="60"
          WidthRequest="300"
          BackgroundColor="LightGray">
        <Label Text="Swipe right"
               HorizontalOptions="Center"
               VerticalOptions="Center" />
    </Grid>
</SwipeView>
```

Il codice C# equivalente è il seguente:

```csharp
// SwipeItems
SwipeItem favoriteSwipeItem = new SwipeItem
{
    Text = "Favorite",
    IconImageSource = "favorite.png",
    BackgroundColor = Color.LightGreen
};
favoriteSwipeItem.Invoked += OnFavoriteSwipeItemInvoked;

SwipeItem deleteSwipeItem = new SwipeItem
{
    Text = "Delete",
    IconImageSource = "delete.png",
    BackgroundColor = Color.LightPink
};
deleteSwipeItem.Invoked += OnDeleteSwipeItemInvoked;

List<SwipeItem> swipeItems = new List<SwipeItem>() { favoriteSwipeItem, deleteSwipeItem };

// SwipeView content
Grid grid = new Grid
{
    HeightRequest = 60,
    WidthRequest = 300,
    BackgroundColor = Color.LightGray
};
grid.Children.Add(new Label
{
    Text = "Swipe right",
    HorizontalOptions = LayoutOptions.Center,
    VerticalOptions = LayoutOptions.Center
});

SwipeView swipeView = new SwipeView
{
    LeftItems = new SwipeItems(swipeItems),
    Content = grid
};
```

In questo esempio, il `SwipeView` contenuto è un [`Grid`](xref:Xamarin.Forms.Grid) oggetto che contiene [`Label`](xref:Xamarin.Forms.Label)un oggetto:

[![Screenshot del contenuto di SwipeView, in iOS e Android](swipeview-images/swipeview-content.png "Contenuto di SwipeView")](swipeview-images/swipeview-content-large.png#lightbox "Contenuto di SwipeView")

Gli elementi swipe vengono usati per eseguire azioni sul `SwipeView` contenuto e vengono rivelati quando il controllo viene sottoposto a scorrimento dal lato sinistro:

[![Screenshot di SwipeView swipe Items, in iOS e Android](swipeview-images/swipeview-swipeitems.png "SwipeView swipe elementi")](swipeview-images/swipeview-swipeitems-large.png#lightbox "SwipeView swipe elementi")

Per impostazione predefinita, un elemento swipe viene eseguito quando viene toccato dall'utente. Tuttavia, è possibile modificare questo comportamento. Per altre informazioni, vedere [modalità di scorrimento](#swipe-mode).

Dopo l'esecuzione di un elemento swipe, gli elementi swipe sono nascosti e `SwipeView` il contenuto viene nuovamente visualizzato. Tuttavia, è possibile modificare questo comportamento. Per altre informazioni, vedere [comportamento di scorrimento rapido](#swipe-behavior).

> [!NOTE]
> Il contenuto e gli elementi di scorrimento possono essere inseriti in linea o definiti come risorse.

## <a name="swipe-items"></a>Scorri elementi

Le `LeftItems`raccolte `RightItems`, `TopItems`, e `BottomItems` sono di tipo `SwipeItems`. La `SwipeItems` classe definisce le proprietà seguenti:

- `Mode`, di tipo `SwipeMode`, che indica l'effetto di un'interazione con scorrimento rapido. Per altre informazioni sulla modalità di scorrimento, vedere [modalità di scorrimento](#swipe-mode).
- `SwipeBehaviorOnInvoked`, di tipo `SwipeBehaviorOnInvoked`, che indica il comportamento `SwipeView` di un oggetto dopo la chiamata di un elemento swipe. Per ulteriori informazioni sul comportamento di scorrimento, vedere [scorrimento del comportamento](#swipe-behavior).

Queste proprietà sono supportate da [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) oggetti, il che significa che possono essere destinazioni di data binding e con stile.

Ogni elemento swipe viene definito come `SwipeItem` oggetto che viene inserito in una delle quattro `SwipeItems` raccolte direzionali. La `SwipeItem` classe deriva dalla [`MenuItem`](xref:Xamarin.Forms.MenuItem) classe e aggiunge i membri seguenti:

- `BackgroundColor` Proprietà, di tipo `Color`, che definisce il colore di sfondo dell'elemento swipe. Questa proprietà è supportata da una proprietà associabile.
- Un `Invoked` evento, che viene generato quando viene eseguito l'elemento swipe.

> [!IMPORTANT]
> La [`MenuItem`](xref:Xamarin.Forms.MenuItem) classe definisce diverse proprietà, tra `Command`cui `CommandParameter`, `IconImageSource`, e `Text`. Queste proprietà possono essere impostate su un `SwipeItem` oggetto per definirne l'aspetto e per definire `ICommand` un oggetto che viene eseguito quando viene richiamato l'elemento swipe. Per ulteriori informazioni, vedere [MenuItem Novell. Forms](~/xamarin-forms/user-interface/menuitem.md).

Nell'esempio seguente vengono illustrati due `SwipeItem` oggetti `LeftItems` nella raccolta di `SwipeView`un oggetto:

```xaml
<SwipeView>
    <SwipeView.LeftItems>
        <SwipeItems>
            <SwipeItem Text="Favorite"
                       IconImageSource="favorite.png"
                       BackgroundColor="LightGreen"
                       Invoked="OnFavoriteSwipeItemInvoked" />
            <SwipeItem Text="Delete"
                       IconImageSource="delete.png"
                       BackgroundColor="LightPink"
                       Invoked="OnDeleteSwipeItemInvoked" />
        </SwipeItems>
    </SwipeView.LeftItems>
    <!-- Content -->
</SwipeView>
```

L'aspetto di ogni `SwipeItem` è definito da una combinazione delle proprietà `Text`, `IconImageSource`e `BackgroundColor` :

[![Screenshot di SwipeView swipe Items, in iOS e Android](swipeview-images/swipeview-swipeitems.png "SwipeView swipe elementi")](swipeview-images/swipeview-swipeitems-large.png#lightbox "SwipeView swipe elementi")

Quando viene `SwipeItem` toccato un oggetto, il `Invoked` relativo evento viene generato e gestito dal relativo gestore eventi registrato. In alternativa, la `Command` proprietà può essere impostata su un' `ICommand` implementazione di che verrà eseguita quando `SwipeItem` viene richiamato.

> [!NOTE]
> Quando l'aspetto di un `SwipeItem` oggetto viene definito solo usando `Text` le `IconImageSource` proprietà o, il contenuto viene sempre centrato.

Oltre a definire gli elementi swipe come `SwipeItem` oggetti, è anche possibile definire visualizzazioni di elementi con scorrimento personalizzato. Per altre informazioni, vedere [elementi swipe personalizzati](#custom-swipe-items).

## <a name="swipe-direction"></a>Direzione scorrimento

`SwipeView`supporta quattro direzioni di scorrimento diverse, con la direzione di scorrimento definita dalla raccolta direzionale `SwipeItems` a cui vengono `SwipeItem` aggiunti gli oggetti. Ogni direzione di scorrimento può mantenere i propri elementi di scorrimento. Ad esempio, nell'esempio seguente viene illustrato `SwipeView` un oggetto i cui elementi swipe dipendono dalla direzione di scorrimento:

```xaml
<SwipeView>
    <SwipeView.LeftItems>
        <SwipeItems>
            <SwipeItem Text="Delete"
                       IconImageSource="delete.png"
                       BackgroundColor="LightPink"
                       Command="{Binding DeleteCommand}" />
        </SwipeItems>
    </SwipeView.LeftItems>
    <SwipeView.RightItems>
        <SwipeItems>
            <SwipeItem Text="Favorite"
                       IconImageSource="favorite.png"
                       BackgroundColor="LightGreen"
                       Command="{Binding FavoriteCommand}" />
            <SwipeItem Text="Share"
                       IconImageSource="share.png"
                       BackgroundColor="LightYellow"
                       Command="{Binding ShareCommand}" />
        </SwipeItems>
    </SwipeView.RightItems>
    <!-- Content -->
</SwipeView>
```

In questo esempio, il `SwipeView` contenuto può essere strisciato a destra o a sinistra. Se si scorre a destra, viene visualizzato l'elemento **Delete** swipe, mentre l'operazione di scorrimento a sinistra Mostra gli elementi **Preferiti** e **Condividi** swipe.

> [!WARNING]
> È possibile impostare una sola istanza `SwipeItems` di una raccolta direzionale alla volta in un oggetto `SwipeView`. Non è pertanto possibile avere due `LeftItems` definizioni in un `SwipeView`oggetto.

Gli `SwipeStarted`eventi `SwipeChanging`, e `SwipeEnded` segnalano la direzione di scorrimento tramite la `SwipeDirection` proprietà negli argomenti dell'evento. Questa proprietà è di tipo `SwipeDirection`, che è un'enumerazione costituita da quattro membri:

- `Right`indica che si è verificato un scorrimento a destra.
- `Left`indica che si è verificato un scorrimento a sinistra.
- `Up`indica che si è verificata una striscia verso l'alto.
- `Down`indica che si è verificato un scorrimento verso il basso.

## <a name="swipe-mode"></a>Modalità di scorrimento

La `SwipeItems` classe dispone di `Mode` una proprietà, che indica l'effetto di un'interazione con scorrimento rapido. Questa proprietà deve essere impostata su uno dei membri `SwipeMode` dell'enumerazione:

- `Reveal`indica che un swipe rivela gli elementi di scorrimento. Questo è il valore predefinito per la proprietà `SwipeItems.Mode`.
- `Execute`indica che un swipe esegue gli elementi di scorrimento.

In modalità Reveal, l'utente scorre un `SwipeView` oggetto per aprire un menu costituito da uno o più elementi swipe e deve toccare in modo esplicito un elemento swipe per eseguirlo. Dopo l'esecuzione dell'elemento swipe, gli elementi swipe vengono chiusi e il `SwipeView` contenuto viene nuovamente visualizzato. In modalità di esecuzione, l'utente scorre un `SwipeView` oggetto per aprire un menu costituito da uno o più elementi di scorrimento, che vengono quindi eseguiti automaticamente. Dopo l'esecuzione, gli elementi swipe vengono chiusi e `SwipeView` il contenuto viene nuovamente visualizzato.

Nell'esempio seguente viene illustrato `SwipeView` un oggetto configurato per l'utilizzo della modalità di esecuzione:

```xaml
<SwipeView>
    <SwipeView.LeftItems>
        <SwipeItems Mode="Execute">
            <SwipeItem Text="Delete"
                       IconImageSource="delete.png"
                       BackgroundColor="LightPink"
                       Command="{Binding DeleteCommand}" />
        </SwipeItems>
    </SwipeView.LeftItems>
    <!-- Content -->
</SwipeView>
```

In questo esempio, il `SwipeView` contenuto può essere sottoposto a scorrimento rapido per rivelare l'elemento swipe, che viene eseguito immediatamente. Dopo l'esecuzione, `SwipeView` il contenuto viene nuovamente visualizzato.

## <a name="swipe-behavior"></a>Comportamento di scorrimento rapido

La `SwipeItems` classe dispone di `SwipeBehaviorOnInvoked` una proprietà, che indica il `SwipeView` comportamento di un oggetto dopo la chiamata di un elemento swipe. Questa proprietà deve essere impostata su uno dei membri `SwipeBehaviorOnInvoked` dell'enumerazione:

- `Auto`indica che in modalità Reveal il `SwipeView` viene chiuso dopo che è stato richiamato un elemento swipe e in `SwipeView` modalità di esecuzione rimane aperto dopo la chiamata di un elemento swipe. Questo è il valore predefinito per la proprietà `SwipeItems.SwipeBehaviorOnInvoked`.
- `Close`indica che il `SwipeView` metodo viene chiuso dopo la chiamata di un elemento swipe.
- `RemainOpen`indica che rimane `SwipeView` aperto dopo la chiamata di un elemento swipe.

Nell'esempio seguente viene illustrato `SwipeView` un oggetto configurato per rimanere aperto dopo che è stato richiamato un elemento swipe:

```xaml
<SwipeView>
    <SwipeView.LeftItems>
        <SwipeItems SwipeBehaviorOnInvoked="RemainOpen">
            <SwipeItem Text="Favorite"
                       IconImageSource="favorite.png"
                       BackgroundColor="LightGreen"
                       Invoked="OnFavoriteSwipeItemInvoked" />
            <SwipeItem Text="Delete"
                       IconImageSource="delete.png"
                       BackgroundColor="LightPink"
                       Invoked="OnDeleteSwipeItemInvoked" />
        </SwipeItems>
    </SwipeView.LeftItems>
    <!-- Content -->
</SwipeView>
```

## <a name="custom-swipe-items"></a>Elementi di scorrimento personalizzati

Gli elementi swipe personalizzati possono essere definiti con `SwipeItemView` il tipo. La `SwipeItemView` classe deriva dalla [`ContentView`](xref:Xamarin.Forms.ContentView) classe e aggiunge le proprietà seguenti:

- `Command`, di tipo `ICommand`, che viene eseguito quando viene toccato un elemento swipe.
- `CommandParameter`, di tipo `object`, ovvero il parametro passato a `Command`.

Queste proprietà sono supportate da [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) oggetti, il che significa che possono essere destinazioni di data binding e con stile.

La `SwipeItemView` classe definisce anche un `Invoked` evento che viene generato quando l'elemento viene toccato, dopo l' `Command` esecuzione di.

Nell'esempio seguente viene illustrato `SwipeItemView` un oggetto nella `LeftItems` raccolta di un `SwipeView`oggetto:

```xaml
<SwipeView>
    <SwipeView.LeftItems>
        <SwipeItems>
            <SwipeItemView Command="{Binding CheckAnswerCommand}"
                           CommandParameter="{Binding Source={x:Reference resultEntry}, Path=Text}">
                <StackLayout Margin="10"
                             WidthRequest="300">
                    <Entry x:Name="resultEntry"
                           Placeholder="Enter answer"
                           HorizontalOptions="CenterAndExpand" />
                    <Label Text="Check"
                           FontAttributes="Bold"
                           HorizontalOptions="Center" />
                </StackLayout>
            </SwipeItemView>
        </SwipeItems>
    </SwipeView.LeftItems>
    <!-- Content -->
</SwipeView>
```

In questo esempio, è `SwipeItemView` costituito [`StackLayout`](xref:Xamarin.Forms.StackLayout) da un [`Entry`](xref:Xamarin.Forms.Entry) oggetto contenente [`Label`](xref:Xamarin.Forms.Label)un oggetto e un oggetto. Dopo che l'utente immette input in `Entry`, è `SwipeViewItem` possibile toccare il resto di, che esegue l'oggetto `ICommand` definito dalla `SwipeItemView.Command` proprietà.

## <a name="open-and-close-a-swipeview-programmatically"></a>Aprire e chiudere un SwipeView a livello di codice

`SwipeView`include `Open` i `Close` metodi e, che aprono e chiudono a livello di codice gli elementi di scorrimento, rispettivamente.

Il `Open` metodo richiede un `OpenSwipeItem` argomento per specificare la direzione da cui `SwipeView` verrà aperto. L' `OpenSwipeItem` enumerazione ha quattro membri:

- `LeftItems`, che indica che l' `SwipeView` oggetto verrà aperto da sinistra per rivelare gli elementi di scorrimento della `LeftItems` raccolta.
- `TopItems`, che indica che l' `SwipeView` oggetto verrà aperto dalla parte superiore, per rivelare gli elementi di scorrimento della `TopItems` raccolta.
- `RightItems`, che indica che l' `SwipeView` oggetto verrà aperto dalla destra, per rivelare gli elementi di scorrimento della `RightItems` raccolta.
- `BottomItems`, che indica che l' `SwipeView` oggetto verrà aperto dalla parte inferiore, per rivelare gli elementi di scorrimento della `BottomItems` raccolta.

Dato un `SwipeView` oggetto `swipeView`denominato, nell'esempio seguente viene illustrato come aprire `SwipeView` un oggetto per rivelare gli elementi di scorrimento `LeftItems` nella raccolta:

```csharp
swipeView.Open(OpenSwipeItem.LeftItems);
```

`swipeView` Può quindi essere chiuso con il `Close` metodo:

```csharp
swipeView.Close();
```

> [!NOTE]
> Quando viene `Close` richiamato il metodo, `CloseRequested` viene generato l'evento.

## <a name="disable-a-swipeview"></a>Disabilitare un SwipeView

Un'applicazione può entrare in uno stato in cui il scorrimento di un elemento di contenuto non è un'operazione valida. In questi casi, `SwipeView` può essere disabilitato impostando la `IsEnabled` relativa proprietà `false`su. In questo modo gli utenti non potranno scorrere rapidamente il contenuto per rivelare gli elementi del dito.

Inoltre `Command` , quando `SwipeItem` `SwipeItemView` `ICommand` si definisce la proprietà di un oggetto o, `CanExecute` è possibile specificare il delegato di per abilitare o disabilitare l'elemento swipe.

## <a name="related-links"></a>Link correlati

- [SwipeView (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-swipeviewdemos/)
- [MenuItem di Xamarin.Forms](~/xamarin-forms/user-interface/menuitem.md)
