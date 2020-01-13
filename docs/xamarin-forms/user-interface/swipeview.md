---
title: Xamarin.Forms SwipeView
description: Xamarin.Forms SwipeView è un controllo contenitore che esegue il wrapping di un elemento di contenuto e fornisce voci di menu di scelta rapida che vengono rivelate da un gesto di scorrimento.
ms.prod: xamarin
ms.assetId: 602456B5-701B-4948-B454-B1F31283F1CF
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/11/2019
ms.openlocfilehash: 4119a650c431013bb0c8e680de600ed4e73d0c93
ms.sourcegitcommit: d0e6436edbf7c52d760027d5e0ccaba2531d9fef
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75490388"
---
# <a name="xamarinforms-swipeview"></a>Xamarin.Forms SwipeView

![](~/media/shared/preview.png "This API is currently pre-release")

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-swipeviewdemos/)

Il `SwipeView` è un controllo contenitore che esegue il wrapping di un elemento di contenuto e fornisce voci di menu di scelta rapida rivelate da un gesto di scorrimento:

[![Screenshot di SwipeView swipe items in a CollectionView, in iOS e Android](swipeview-images/swipeview-collectionview.png "SwipeView swipe elementi")](swipeview-images/swipeview-collectionview-large.png#lightbox "SwipeView swipe elementi")

`SwipeView` è disponibile in Xamarin.Forms 4,4. Tuttavia, è attualmente sperimentale e può essere usata solo aggiungendo la riga di codice seguente alla classe `AppDelegate` in iOS, alla classe `MainActivity` in Android o alla classe di `App` in UWP, prima di chiamare `Forms.Init`:

```csharp
Forms.SetFlags("SwipeView_Experimental");
```

`SwipeView` definisce le proprietà seguenti:

- `LeftItems`, di tipo `SwipeItems`, che rappresenta gli elementi swipe che possono essere richiamati quando il controllo viene sottoposto a scorrimento dal lato sinistro.
- `RightItems`, di tipo `SwipeItems`, che rappresenta gli elementi swipe che possono essere richiamati quando il controllo viene sottoposto a scorrimento dal lato destro.
- `TopItems`, di tipo `SwipeItems`, che rappresenta gli elementi swipe che possono essere richiamati quando il controllo viene sottoposto a scorrimento dall'alto verso il basso.
- `BottomItems`, di tipo `SwipeItems`, che rappresenta gli elementi swipe che possono essere richiamati quando il controllo viene sottoposto a scorrimento dal basso verso l'alto.

Queste proprietà sono supportate da oggetti [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) , il che significa che possono essere destinazioni di data binding e con stile.

Inoltre, il `SwipeView` eredita la proprietà [`Content`](xref:Xamarin.Forms.ContentView.Content) dalla classe [`ContentView`](xref:Xamarin.Forms.ContentView) . La proprietà `Content` è la proprietà Content della classe `SwipeView` e pertanto non è necessario impostarla in modo esplicito.

La classe `SwipeView` definisce anche quattro eventi:

- `SwipeStarted` viene generato all'avvio di un swipe. L'oggetto `SwipeStartedEventArgs` che accompagna questo evento dispone di una proprietà `SwipeDirection`, di tipo `SwipeDirection`.
- `SwipeChanging` viene attivato quando si sposta il dito. L'oggetto `SwipeChangingEventArgs` che accompagna questo evento dispone di una proprietà `SwipeDirection`, di tipo `SwipeDirection`e di una proprietà `Offset` di tipo `double`.
- `SwipeEnded` viene generato al termine di un swipe. L'oggetto `SwipeEndedEventArgs` che accompagna questo evento dispone di una proprietà `SwipeDirection`, di tipo `SwipeDirection`.
- `CloseRequested` viene generato quando gli elementi di scorrimento vengono chiusi.

Inoltre, `SwipeView` definisce un metodo `Close`, che chiude gli elementi di scorrimento.

> [!NOTE]
> `SwipeView` dispone di una piattaforma specifica in iOS e Android, che controlla la transizione utilizzata per l'apertura di un `SwipeView`. Per altre informazioni, vedere [modalità di transizione SwipeView swipe in iOS](~/xamarin-forms/platform/ios/swipeview-swipetransitionmode.md) e la [modalità di transizione SwipeView swipe in Android](~/xamarin-forms/platform/android/swipeview-swipetransitionmode.md).

## <a name="create-a-swipeview"></a>Creare un SwipeView

Un `SwipeView` deve definire il contenuto a cui il `SwipeView` esegue il wrapping e gli elementi di scorrimento che vengono rivelati dal gesto di scorrimento. Gli elementi swipe sono uno o più oggetti `SwipeItem` posizionati in una delle quattro raccolte `SwipeView` direzionali: `LeftItems`, `RightItems`, `TopItems`o `BottomItems`.

Nell'esempio seguente viene mostrato come creare un `SwipeView` in XAML:

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

In questo esempio, il contenuto del `SwipeView` è un [`Grid`](xref:Xamarin.Forms.Grid) che contiene un [`Label`](xref:Xamarin.Forms.Label):

[![Screenshot del contenuto di SwipeView, in iOS e Android](swipeview-images/swipeview-content.png "Contenuto di SwipeView")](swipeview-images/swipeview-content-large.png#lightbox "Contenuto di SwipeView")

Gli elementi swipe vengono usati per eseguire azioni sul contenuto `SwipeView` e vengono rivelati quando il controllo viene sottoposto a scorrimento dal lato sinistro:

[![Screenshot di SwipeView swipe Items, in iOS e Android](swipeview-images/swipeview-swipeitems.png "SwipeView swipe elementi")](swipeview-images/swipeview-swipeitems-large.png#lightbox "SwipeView swipe elementi")

Per impostazione predefinita, un elemento swipe viene eseguito quando viene toccato dall'utente. Tuttavia, è possibile modificare questo comportamento. Per altre informazioni, vedere [modalità di scorrimento](#swipe-mode).

Dopo l'esecuzione di un elemento swipe, gli elementi swipe sono nascosti e il contenuto del `SwipeView` viene nuovamente visualizzato. Tuttavia, è possibile modificare questo comportamento. Per altre informazioni, vedere [comportamento di scorrimento rapido](#swipe-behavior).

> [!NOTE]
> Il contenuto e gli elementi di scorrimento possono essere inseriti in linea o definiti come risorse.

## <a name="swipe-items"></a>Scorri elementi

Le raccolte `LeftItems`, `RightItems`, `TopItems`e `BottomItems` sono di tipo `SwipeItems`. La classe `SwipeItems` definisce le proprietà seguenti:

- `Mode`, di tipo `SwipeMode`, che indica l'effetto di un'interazione con scorrimento rapido. Per altre informazioni sulla modalità di scorrimento, vedere [modalità di scorrimento](#swipe-mode).
- `SwipeBehaviorOnInvoked`, di tipo `SwipeBehaviorOnInvoked`, che indica il comportamento di un `SwipeView` dopo la chiamata di un elemento swipe. Per ulteriori informazioni sul comportamento di scorrimento, vedere [scorrimento del comportamento](#swipe-behavior).

Queste proprietà sono supportate da oggetti [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) , il che significa che possono essere destinazioni di data binding e con stile.

Ogni elemento swipe viene definito come oggetto `SwipeItem` inserito in una delle quattro raccolte `SwipeItems` direzionali. La classe `SwipeItem` deriva dalla classe [`MenuItem`](xref:Xamarin.Forms.MenuItem) e aggiunge i membri seguenti:

- Proprietà `BackgroundColor`, di tipo `Color`, che definisce il colore di sfondo dell'elemento swipe. Questa proprietà è supportata da una proprietà associabile.
- Evento `Invoked`, che viene generato quando viene eseguito l'elemento swipe.

> [!IMPORTANT]
> La classe [`MenuItem`](xref:Xamarin.Forms.MenuItem) definisce diverse proprietà, tra cui `Command`, `CommandParameter`, `IconImageSource`e `Text`. Queste proprietà possono essere impostate su un oggetto `SwipeItem` per definirne l'aspetto e per definire un `ICommand` eseguito quando viene richiamato l'elemento swipe. Per ulteriori informazioni, vedere [MenuItem Xamarin.Forms](~/xamarin-forms/user-interface/menuitem.md).

Nell'esempio seguente vengono illustrati due oggetti `SwipeItem` nella raccolta `LeftItems` di un `SwipeView`:

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

L'aspetto di ogni `SwipeItem` viene definito dalle proprietà `Text`, `IconImageSource`e `BackgroundColor`:

[![Screenshot di SwipeView swipe Items, in iOS e Android](swipeview-images/swipeview-swipeitems.png "SwipeView swipe elementi")](swipeview-images/swipeview-swipeitems-large.png#lightbox "SwipeView swipe elementi")

Quando viene toccato un `SwipeItem`, il relativo evento `Invoked` viene generato e gestito dal relativo gestore eventi registrato. In alternativa, la proprietà `Command` può essere impostata su un'implementazione di `ICommand` che verrà eseguita quando viene richiamato il `SwipeItem`.

> [!NOTE]
> Oltre a definire gli elementi di scorrimento come `SwipeItem` oggetti, è anche possibile definire visualizzazioni di elementi con scorrimento personalizzato. Per altre informazioni, vedere [elementi swipe personalizzati](#custom-swipe-items).

## <a name="swipe-direction"></a>Direzione scorrimento

`SwipeView` supporta quattro direzioni di scorrimento diverse, con la direzione di scorrimento definita dalla raccolta di `SwipeItems` direzionali a cui vengono aggiunti gli oggetti di `SwipeItem`. Ogni direzione di scorrimento può mantenere i propri elementi di scorrimento. Ad esempio, nell'esempio seguente viene illustrato un `SwipeView` i cui elementi swipe dipendono dalla direzione di scorrimento:

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

In questo esempio, è possibile scorrere il contenuto del `SwipeView` a destra o a sinistra. Se si scorre a destra, viene visualizzato l'elemento **Delete** swipe, mentre l'operazione di scorrimento a sinistra Mostra gli elementi **Preferiti** e **Condividi** swipe.

> [!WARNING]
> È possibile impostare una sola istanza di una raccolta di `SwipeItems` direzionali alla volta in un `SwipeView`. Non è pertanto possibile avere due definizioni di `LeftItems` in un `SwipeView`.

Gli eventi `SwipeStarted`, `SwipeChanging`e `SwipeEnded` segnalano la direzione di scorrimento tramite la proprietà `SwipeDirection` negli argomenti dell'evento. Questa proprietà è di tipo `SwipeDirection`, ovvero un'enumerazione costituita da quattro membri:

- `Right` indica che si è verificato un scorrimento a destra.
- `Left` indica che si è verificato un scorrimento a sinistra.
- `Up` indica che si è verificata una striscia verso l'alto.
- `Down` indica che si è verificata una striscia verso il basso.

## <a name="swipe-mode"></a>Modalità di scorrimento

La classe `SwipeItems` dispone di una proprietà `Mode`, che indica l'effetto di un'interazione con scorrimento rapido. Questa proprietà deve essere impostata su uno dei membri dell'enumerazione `SwipeMode`:

- `Reveal` indica che un swipe rivela gli elementi di scorrimento. Questo è il valore predefinito per la proprietà `SwipeItems.Mode`.
- `Execute` indica che un swipe esegue gli elementi di scorrimento.

In modalità Reveal, l'utente scorre un `SwipeView` per aprire un menu costituito da uno o più elementi swipe e deve toccare in modo esplicito un elemento swipe per eseguirlo. Dopo l'esecuzione dell'elemento swipe, gli elementi swipe vengono chiusi e il contenuto del `SwipeView` viene nuovamente visualizzato. In modalità di esecuzione, l'utente scorre un `SwipeView` per aprire un menu costituito da uno o più elementi swipe, che vengono quindi eseguiti automaticamente. Dopo l'esecuzione, gli elementi swipe vengono chiusi e il contenuto del `SwipeView` viene nuovamente visualizzato.

Nell'esempio seguente viene illustrato un `SwipeView` configurato per utilizzare la modalità di esecuzione:

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

In questo esempio, il contenuto del `SwipeView` può essere sottoposto a scorrimento rapido per rivelare l'elemento swipe, che viene eseguito immediatamente. Dopo l'esecuzione, il contenuto del `SwipeView` viene nuovamente visualizzato.

## <a name="swipe-behavior"></a>Comportamento di scorrimento rapido

La classe `SwipeItems` dispone di una proprietà `SwipeBehaviorOnInvoked`, che indica il comportamento di un `SwipeView` dopo la chiamata di un elemento swipe. Questa proprietà deve essere impostata su uno dei membri dell'enumerazione `SwipeBehaviorOnInvoked`:

- `Auto` indica che in modalità di rivelazione l'`SwipeView` si chiude dopo che è stato richiamato un elemento swipe e in modalità di esecuzione il `SwipeView` rimane aperto dopo la chiamata di un elemento swipe. Questo è il valore predefinito per la proprietà `SwipeItems.SwipeBehaviorOnInvoked`.
- `Close` indica che il `SwipeView` viene chiuso dopo la chiamata di un elemento swipe.
- `RemainOpen` indica che il `SwipeView` rimane aperto dopo la chiamata di un elemento swipe.

Nell'esempio seguente viene illustrato un `SwipeView` configurato per rimanere aperto dopo che è stato richiamato un elemento swipe:

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

Gli elementi swipe personalizzati possono essere definiti con il tipo di `SwipeItemView`. La classe `SwipeItemView` deriva dalla classe [`ContentView`](xref:Xamarin.Forms.ContentView) e aggiunge le proprietà seguenti:

- `Command`, di tipo `ICommand`, che viene eseguito quando viene toccato un elemento swipe.
- `CommandParameter`, di tipo `object`, ovvero il parametro passato a `Command`.

Queste proprietà sono supportate da oggetti [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) , il che significa che possono essere destinazioni di data binding e con stile.

La classe `SwipeItemView` definisce anche un evento `Invoked` generato quando l'elemento viene toccato, dopo l'esecuzione del `Command`.

Nell'esempio seguente viene illustrato un oggetto `SwipeItemView` nella raccolta `LeftItems` di un `SwipeView`:

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

In questo esempio, il `SwipeItemView` è costituito da un [`StackLayout`](xref:Xamarin.Forms.StackLayout) contenente un [`Entry`](xref:Xamarin.Forms.Entry) e un [`Label`](xref:Xamarin.Forms.Label). Dopo che l'utente immette l'input nella `Entry`, è possibile toccare il resto del `SwipeViewItem` che esegue il `ICommand` definito dalla proprietà `SwipeItemView.Command`.

## <a name="disable-a-swipeview"></a>Disabilitare un SwipeView

Un'applicazione può entrare in uno stato in cui il scorrimento di un elemento di contenuto non è un'operazione valida. In questi casi, è possibile disabilitare il `SwipeView` impostando la relativa proprietà `IsEnabled` su `false`. In questo modo gli utenti non potranno scorrere rapidamente il contenuto per rivelare gli elementi del dito.

Inoltre, quando si definisce la proprietà `Command` di un `SwipeItem` o `SwipeItemView`, è possibile specificare il delegato `CanExecute` del `ICommand` per abilitare o disabilitare l'elemento swipe.

## <a name="related-links"></a>Collegamenti correlati

- [SwipeView (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-swipeviewdemos/)
- [MenuItem Xamarin.Forms](~/xamarin-forms/user-interface/menuitem.md)
