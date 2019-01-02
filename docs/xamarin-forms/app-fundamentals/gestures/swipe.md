---
title: Aggiunta di un sistema di riconoscimento del movimento di scorrimento rapido
description: Questo articolo spiega come riconoscere un movimento di scorrimento rapido che si verifica in una vista.
ms.prod: xamarin
ms.assetid: 164976C2-1429-49FB-9EB6-621E2681C19B
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/14/2018
ms.openlocfilehash: 2e28d3e7035eb570b2053d39cec8b8d91dada6f0
ms.sourcegitcommit: be6f6a8f77679bb9675077ed25b5d2c753580b74
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/07/2018
ms.locfileid: "53059418"
---
# <a name="adding-a-swipe-gesture-recognizer"></a>Aggiunta di un sistema di riconoscimento del movimento di scorrimento rapido

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://developer.xamarin.com/samples/xamarin-forms/WorkingWithGestures/SwipeGesture/)

_Il movimento di scorrimento rapido si verifica quando si muove un dito sullo schermo in senso orizzontale o verticale e viene spesso usato per avviare la navigazione nel contenuto. Gli esempi di codice in questo articolo sono tratti da [SwipeGesture](https://developer.xamarin.com/samples/xamarin-forms/WorkingWithGestures/SwipeGesture/)._

Per fare in modo che un elemento [`View`](xref:Xamarin.Forms.View) riconosca un movimento di scorrimento rapido, creare un'istanza di [`SwipeGestureRecognizer`](xref:Xamarin.Forms.SwipeGestureRecognizer), impostare la proprietà [`Direction`](xref:Xamarin.Forms.SwipeGestureRecognizer.Direction) su un valore di enumerazione [`SwipeDirection`](xref:Xamarin.Forms.SwipeDirection) (`Left`, `Right`, `Up` o `Down`), se necessario impostare la proprietà [`Threshold`](xref:Xamarin.Forms.SwipeGestureRecognizer.Threshold), gestire l'evento [`Swiped`](xref:Xamarin.Forms.SwipeGestureRecognizer.Swiped) e aggiungere il nuovo sistema di riconoscimento del movimento alla raccolta [`GestureRecognizers`](xref:Xamarin.Forms.View.GestureRecognizers) per la vista. L'esempio di codice seguente visualizza un `SwipeGestureRecognizer` associato a un elemento [`BoxView`](xref:Xamarin.Forms.BoxView):

```xaml
<BoxView Color="Teal" ...>
    <BoxView.GestureRecognizers>
        <SwipeGestureRecognizer Direction="Left" Swiped="OnSwiped"/>
    </BoxView.GestureRecognizers>
</BoxView>
```

Questo è il codice C# equivalente:

```csharp
var boxView = new BoxView { Color = Color.Teal, ... };
var leftSwipeGesture = new SwipeGestureRecognizer { Direction = SwipeDirection.Left };
leftSwipeGesture.Swiped += OnSwiped;

boxView.GestureRecognizers.Add(leftSwipeGesture);
```

La classe [`SwipeGestureRecognizer`](xref:Xamarin.Forms.SwipeGestureRecognizer) include anche una proprietà [`Threshold`](xref:Xamarin.Forms.SwipeGestureRecognizer.Threshold), che si può impostare su un valore `uint` che rappresenta la distanza di scorrimento minima che si deve raggiungere affinché lo scorrimento rapido venga riconosciuto, in unità indipendenti dal dispositivo. Il valore predefinito di questa proprietà è 100, ovvero tutti gli scorrimenti rapidi inferiori a 100 unità indipendenti dal dispositivo verranno ignorati.

## <a name="recognizing-the-swipe-direction"></a>Riconoscimento della direzione di scorrimento

Negli esempi precedenti la proprietà [`Direction`](xref:Xamarin.Forms.SwipedEventArgs.Direction) viene impostata un singolo valore dell'enumerazione [`SwipeDirection`](xref:Xamarin.Forms.SwipeDirection). Tuttavia, è anche possibile impostare questa proprietà su più valori dell'enumerazione `SwipeDirection`, in modo che l'evento [`Swiped`](xref:Xamarin.Forms.SwipeGestureRecognizer.Swiped) venga attivato in risposta a uno scorrimento rapido in più di una direzione. Esiste comunque un vincolo per cui un singolo elemento [`SwipeGestureRecognizer`](xref:Xamarin.Forms.SwipeGestureRecognizer) è in grado di riconoscere solo gli scorrimenti che avvengono sullo stesso asse. Di conseguenza, gli scorrimenti rapidi che si verificano sull'asse orizzontale possono essere riconosciuti impostando la proprietà `Direction` su `Left` e `Right`:

```xaml
<SwipeGestureRecognizer Direction="Left,Right" Swiped="OnSwiped"/>
```

Analogamente, gli scorrimenti rapidi che si verificano sull'asse verticale possono essere riconosciuti impostando la proprietà [`Direction`](xref:Xamarin.Forms.SwipedEventArgs.Direction) su `Up` e `Down`:

```csharp
var swipeGesture = new SwipeGestureRecognizer { Direction = SwipeDirection.Up | SwipeDirection.Down };
```

In alternativa, è possibile creare un elemento [`SwipeGestureRecognizer`](xref:Xamarin.Forms.SwipeGestureRecognizer) per ogni direzione di scorrimento in modo da riconoscere gli scorrimenti in ogni direzione:

```xaml
<BoxView Color="Teal" ...>
    <BoxView.GestureRecognizers>
        <SwipeGestureRecognizer Direction="Left" Swiped="OnSwiped"/>
        <SwipeGestureRecognizer Direction="Right" Swiped="OnSwiped"/>
        <SwipeGestureRecognizer Direction="Up" Swiped="OnSwiped"/>
        <SwipeGestureRecognizer Direction="Down" Swiped="OnSwiped"/>
    </BoxView.GestureRecognizers>
</BoxView>
```

Questo è il codice C# equivalente:

```csharp
var boxView = new BoxView { Color = Color.Teal, ... };
var leftSwipeGesture = new SwipeGestureRecognizer { Direction = SwipeDirection.Left };
leftSwipeGesture.Swiped += OnSwiped;
var rightSwipeGesture = new SwipeGestureRecognizer { Direction = SwipeDirection.Right };
rightSwipeGesture.Swiped += OnSwiped;
var upSwipeGesture = new SwipeGestureRecognizer { Direction = SwipeDirection.Up };
upSwipeGesture.Swiped += OnSwiped;
var downSwipeGesture = new SwipeGestureRecognizer { Direction = SwipeDirection.Down };
downSwipeGesture.Swiped += OnSwiped;

boxView.GestureRecognizers.Add(leftSwipeGesture);
boxView.GestureRecognizers.Add(rightSwipeGesture);
boxView.GestureRecognizers.Add(upSwipeGesture);
boxView.GestureRecognizers.Add(downSwipeGesture);
```

> [!NOTE]
> Negli esempi precedenti lo stesso gestore eventi risponde all'attivazione dell'evento [`Swiped`](xref:Xamarin.Forms.SwipeGestureRecognizer.Swiped). Tuttavia, ogni istanza di [`SwipeGestureRecognizer`](xref:Xamarin.Forms.SwipeGestureRecognizer) può usare un diverso gestore eventi se necessario.

## <a name="responding-to-the-swipe"></a>Risposta allo scorrimento rapido

Nell'esempio che segue è illustrato un gestore eventi per l'evento [`Swiped`](xref:Xamarin.Forms.SwipeGestureRecognizer.Swiped):

```csharp
void OnSwiped(object sender, SwipedEventArgs e)
{
    switch (e.Direction)
    {
        case SwipeDirection.Left:
            // Handle the swipe
            break;
        case SwipeDirection.Right:
            // Handle the swipe
            break;
        case SwipeDirection.Up:
            // Handle the swipe
            break;
        case SwipeDirection.Down:
            // Handle the swipe
            break;
    }
}
```

L'elemento [`SwipedEventArgs`](xref:Xamarin.Forms.SwipedEventArgs) può essere esaminato per determinare la direzione di scorrimento, con la logica personalizzata che risponde allo scorrimento rapido come richiesto. La direzione dello scorrimento rapido può essere ottenuta dalla proprietà [`Direction`](xref:Xamarin.Forms.SwipedEventArgs.Direction) degli argomenti dell'evento, che sarà impostata su uno dei valori dell'enumerazione [`SwipeDirection`](xref:Xamarin.Forms.SwipeDirection). Inoltre, gli argomenti dell'evento usano anche una proprietà [`Parameter`](xref:Xamarin.Forms.SwipedEventArgs.Parameter) che verrà impostata sul valore della proprietà [`CommandParameter`](xref:Xamarin.Forms.SwipeGestureRecognizer.CommandParameter), se definito.

## <a name="using-commands"></a>Uso dei comandi

La classe [`SwipeGestureRecognizer`](xref:Xamarin.Forms.SwipeGestureRecognizer) include anche le proprietà [`Command`](xref:Xamarin.Forms.SwipeGestureRecognizer.Command) e [`CommandParameter`](xref:Xamarin.Forms.SwipeGestureRecognizer.CommandParameter). Queste proprietà in genere vengono usate nelle applicazioni che usano il modello Model-View-ViewModel (MVVM). La proprietà `Command` definisce l'elemento `ICommand` da richiamare quando viene riconosciuto un movimento di scorrimento rapido, con la proprietà `CommandParameter` che definisce un oggetto da passare a `ICommand.`. L'esempio di codice seguente illustra come associare la proprietà `Command` a un elemento `ICommand` definito nel modello di visualizzazione la cui istanza è impostata come pagina [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext):

```csharp
var boxView = new BoxView { Color = Color.Teal, ... };
var leftSwipeGesture = new SwipeGestureRecognizer { Direction = SwipeDirection.Left, CommandParameter = "Left" };
leftSwipeGesture.SetBinding(SwipeGestureRecognizer.CommandProperty, "SwipeCommand");
boxView.GestureRecognizers.Add(leftSwipeGesture);
```

Il codice XAML equivalente è:

```xaml
<BoxView Color="Teal" ...>
    <BoxView.GestureRecognizers>
        <SwipeGestureRecognizer Direction="Left" Command="{Binding SwipeCommand}" CommandParameter="Left" />
    </BoxView.GestureRecognizers>
</BoxView>
```

`SwipeCommand` è una proprietà di tipo `ICommand` definita nell'istanza del modello di visualizzazione impostato come pagina [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext). Quando viene riconosciuto un movimento di scorrimento rapido, viene eseguito il metodo `Execute` dell'oggetto `SwipeCommand`. L'argomento per il metodo `Execute` è il valore della proprietà [`CommandParameter`](xref:Xamarin.Forms.SwipeGestureRecognizer.CommandParameter). Per altre informazioni sui comandi, vedere l'articolo sull'[interfaccia di comando](~/xamarin-forms/app-fundamentals/data-binding/commanding.md).

## <a name="creating-a-swipe-container"></a>Creazione di un contenitore dello scorrimento rapido

La classe `SwipeContainer`, illustrata nell'esempio di codice seguente, è una classe di riconoscimento dello scorrimento rapido generalizzata di cui viene eseguito il wrapping intorno a un oggetto [`View`](xref:Xamarin.Forms.View) ai fini del riconoscimento del movimento di scorrimento rapido:

```csharp
public class SwipeContainer : ContentView
{
    public event EventHandler<SwipedEventArgs> Swipe;

    public SwipeContainer()
    {
        GestureRecognizers.Add(GetSwipeGestureRecognizer(SwipeDirection.Left));
        GestureRecognizers.Add(GetSwipeGestureRecognizer(SwipeDirection.Right));
        GestureRecognizers.Add(GetSwipeGestureRecognizer(SwipeDirection.Up));
        GestureRecognizers.Add(GetSwipeGestureRecognizer(SwipeDirection.Down));
    }

    SwipeGestureRecognizer GetSwipeGestureRecognizer(SwipeDirection direction)
    {
        var swipe = new SwipeGestureRecognizer { Direction = direction };
        swipe.Swiped += (sender, e) => Swipe?.Invoke(this, e);
        return swipe;
    }
}
```

La classe `SwipeContainer` crea oggetti [`SwipeGestureRecognizer`](xref:Xamarin.Forms.SwipeGestureRecognizer) per tutte e quattro le direzioni di scorrimento e associa gestori eventi `Swipe`. Questi gestori eventi richiamano l'evento `Swipe` definito da `SwipeContainer`.

L'esempio di codice XAML seguente illustra il wrapping della classe `SwipeContainer` intorno a un oggetto [`BoxView`](xref:Xamarin.Forms.BoxView):

```xaml
<ContentPage ...>
    <StackLayout>
        <local:SwipeContainer Swipe="OnSwiped" ...>
            <BoxView Color="Teal" ... />
        </local:SwipeContainer>
    </StackLayout>
</ContentPage>
```

L'esempio di codice seguente illustra come `SwipeContainer` esegue il wrapping di un oggetto [`BoxView`](xref:Xamarin.Forms.BoxView) in una pagina C#:

```csharp
public class SwipeContainerPageCS : ContentPage
{
    public SwipeContainerPageCS()
    {
        var boxView = new BoxView { Color = Color.Teal, ... };
        var swipeContainer = new SwipeContainer { Content = boxView, ... };
        swipeContainer.Swipe += (sender, e) =>
        {
          // Handle the swipe
        };

        Content = new StackLayout
        {
            Children = { swipeContainer }
        };
    }
}
```

Quando [`BoxView`](xref:Xamarin.Forms.BoxView) riceve un movimento di scorrimento rapido, l'evento [`Swiped`](xref:Xamarin.Forms.SwipeGestureRecognizer.Swiped) viene attivato in [`SwipeGestureRecognizer`](xref:Xamarin.Forms.SwipeGestureRecognizer). Questa operazione è gestita dalla classe `SwipeContainer` che attiva il proprio evento `Swipe`. L'evento `Swipe` viene gestito nella pagina. L'elemento [`SwipedEventArgs`](xref:Xamarin.Forms.SwipedEventArgs) può quindi essere esaminato per determinare la direzione di scorrimento, con la logica personalizzata che risponde allo scorrimento rapido come richiesto.

## <a name="related-links"></a>Collegamenti correlati

- [Swipe Gesture (esempio)](https://developer.xamarin.com/samples/xamarin-forms/WorkingWithGestures/SwipeGesture/)
- [GestureRecognizer](xref:Xamarin.Forms.GestureRecognizer)
- [SwipeGestureRecognizer](xref:Xamarin.Forms.SwipeGestureRecognizer)
