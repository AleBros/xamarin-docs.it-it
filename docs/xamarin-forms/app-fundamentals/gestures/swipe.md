---
title: Aggiunta di un riconoscimento di movimento scorrimento rapido
description: Questo articolo spiega come riconoscere un gesto di scorrimento rapido che si verificano in una vista.
ms.prod: xamarin
ms.assetid: 164976C2-1429-49FB-9EB6-621E2681C19B
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/14/2018
ms.openlocfilehash: 95e95d8849824cd2dc31c2019627cc5adbbefeec
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2018
ms.locfileid: "50131525"
---
# <a name="adding-a-swipe-gesture-recognizer"></a>Aggiunta di un riconoscimento di movimento scorrimento rapido

_Un gesto di scorrimento rapido si verifica quando un dito viene spostato sullo schermo in senso orizzontale o verticale e viene spesso usato per avviare la navigazione tra il contenuto. Gli esempi di codice in questo articolo vengono prelevati i [gesto di scorrimento rapido](https://developer.xamarin.com/samples/xamarin-forms/WorkingWithGestures/SwipeGesture/) esempio._

Per rendere un [ `View` ](xref:Xamarin.Forms.View) riconosce un gesto di scorrimento rapido, creare un [ `SwipeGestureRecognizer` ](xref:Xamarin.Forms.SwipeGestureRecognizer) dell'istanza, impostare il [ `Direction` ](xref:Xamarin.Forms.SwipeGestureRecognizer.Direction) proprietà su un [ `SwipeDirection` ](xref:Xamarin.Forms.SwipeDirection) valore di enumerazione (`Left`, `Right`, `Up`, o `Down`), facoltativamente, impostare il [ `Threshold` ](xref:Xamarin.Forms.SwipeGestureRecognizer.Threshold) proprietà, handle di [ `Swiped` ](xref:Xamarin.Forms.SwipeGestureRecognizer.Swiped) evento, e aggiungere il nuovo riconoscitore di movimento per il [ `GestureRecognizers` ](xref:Xamarin.Forms.View.GestureRecognizers) raccolta nella vista. Nell'esempio di codice riportato di seguito viene illustrato un `SwipeGestureRecognizer` collegato a un [ `BoxView` ](xref:Xamarin.Forms.BoxView):

```xaml
<BoxView Color="Teal" ...>
    <BoxView.GestureRecognizers>
        <SwipeGestureRecognizer Direction="Left" Swiped="OnSwiped"/>
    </BoxView.GestureRecognizers>
</BoxView>
```

Ecco l'equivalente C# code:

```csharp
var boxView = new BoxView { Color = Color.Teal, ... };
var leftSwipeGesture = new SwipeGestureRecognizer { Direction = SwipeDirection.Left };
leftSwipeGesture.Swiped += OnSwiped;

boxView.GestureRecognizers.Add(leftSwipeGesture);
```

Il [ `SwipeGestureRecognizer` ](xref:Xamarin.Forms.SwipeGestureRecognizer) classe include anche una [ `Threshold` ](xref:Xamarin.Forms.SwipeGestureRecognizer.Threshold) proprietà, che può essere facoltativamente impostato su un `uint` valore che rappresenta la distanza minima scorrere rapidamente che deve essere raggiunta per un scorrere rapidamente per essere riconosciuta, in unità indipendenti dal dispositivo. Il valore predefinito di questa proprietà è 100, il che significa che qualsiasi tessere magnetiche che sono che meno di 100 unità di misura indipendenti dal dispositivo verrà ignorato.

## <a name="recognizing-the-swipe-direction"></a>Riconoscendo la direzione di scorrimento rapido

Negli esempi precedenti, il [ `Direction` ](xref:Xamarin.Forms.SwipedEventArgs.Direction) viene impostata su single, un valore compreso il [ `SwipeDirection` ](xref:Xamarin.Forms.SwipeDirection) enumerazione. Tuttavia, è anche possibile impostare questa proprietà su più valori dal `SwipeDirection` enumerazione, in modo che il [ `Swiped` ](xref:Xamarin.Forms.SwipeGestureRecognizer.Swiped) evento viene generato in risposta a un passaggio del dito in più di una sola direzione. Tuttavia, il vincolo è che una singola [ `SwipeGestureRecognizer` ](xref:Xamarin.Forms.SwipeGestureRecognizer) grado di riconoscere solo tessere magnetiche che si verificano sullo stesso asse. Pertanto, tessere magnetiche che si verificano sull'asse orizzontale possono essere riconosciuti impostando il `Direction` proprietà `Left` e `Right`:

```xaml
<SwipeGestureRecognizer Direction="Left,Right" Swiped="OnSwiped"/>
```

Analogamente, possono essere riconosciuti tessere magnetiche che si verificano sull'asse verticale impostando il [ `Direction` ](xref:Xamarin.Forms.SwipedEventArgs.Direction) proprietà `Up` e `Down`:

```csharp
var swipeGesture = new SwipeGestureRecognizer { Direction = SwipeDirection.Up | SwipeDirection.Down };
```

In alternativa, un [ `SwipeGestureRecognizer` ](xref:Xamarin.Forms.SwipeGestureRecognizer) per scorrere ogni direzione è possibile creare in modo che riconosca tessere magnetiche in ciascuna direzione:

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

Ecco l'equivalente C# code:

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
> Negli esempi precedenti, lo stesso gestore eventi risponde per il [ `Swiped` ](xref:Xamarin.Forms.SwipeGestureRecognizer.Swiped) generazione dell'evento. Tuttavia, ogni [ `SwipeGestureRecognizer` ](xref:Xamarin.Forms.SwipeGestureRecognizer) istanza può usare un diverso gestore eventi se necessario.

## <a name="responding-to-the-swipe"></a>Rispondere allo sfioramento

Un gestore eventi per il [ `Swiped` ](xref:Xamarin.Forms.SwipeGestureRecognizer.Swiped) evento può essere visualizzato nell'esempio seguente:

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

Il [ `SwipedEventArgs` ](xref:Xamarin.Forms.SwipedEventArgs) possono essere esaminate per determinare la direzione dello sfioramento, con la logica personalizzata risponde allo sfioramento in base alle esigenze. La direzione dello sfioramento può essere ottenuta dal [ `Direction` ](xref:Xamarin.Forms.SwipedEventArgs.Direction) degli argomenti dell'evento, che sarà impostato su uno dei valori delle proprietà il [ `SwipeDirection` ](xref:Xamarin.Forms.SwipeDirection) enumerazione. Inoltre, gli argomenti dell'evento presentare una [ `Parameter` ](xref:Xamarin.Forms.SwipedEventArgs.Parameter) che verrà impostato sul valore della proprietà il [ `CommandParameter` ](xref:Xamarin.Forms.SwipeGestureRecognizer.CommandParameter) proprietà, se definito.

## <a name="using-commands"></a>Uso dei comandi

Il [ `SwipeGestureRecognizer` ](xref:Xamarin.Forms.SwipeGestureRecognizer) classe include anche [ `Command` ](xref:Xamarin.Forms.SwipeGestureRecognizer.Command) e [ `CommandParameter` ](xref:Xamarin.Forms.SwipeGestureRecognizer.CommandParameter) proprietà. Queste proprietà vengono usate in genere nelle applicazioni che usano il modello Model-View-ViewModel (MVVM). Il `Command` proprietà definisce il `ICommand` da richiamare quando viene riconosciuto un gesto di scorrimento rapido, con la `CommandParameter` proprietà che definisce un oggetto deve essere passato il `ICommand.` il codice seguente viene illustrato come associare il `Command` proprietà a un `ICommand` definito nel modello di visualizzazione cui istanza viene impostato come la pagina [ `BindingContext` ](xref:Xamarin.Forms.BindableObject.BindingContext):

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

`SwipeCommand` è una proprietà di tipo `ICommand` definito nell'istanza del modello di visualizzazione che viene impostato come pagina [ `BindingContext` ](xref:Xamarin.Forms.BindableObject.BindingContext). Quando un dito viene riconosciuto, il `Execute` metodo del `SwipeCommand` oggetto verrà eseguito. L'argomento per il `Execute` metodo è il valore della [ `CommandParameter` ](xref:Xamarin.Forms.SwipeGestureRecognizer.CommandParameter) proprietà. Per altre informazioni sui comandi, vedere [l'interfaccia di comando](~/xamarin-forms/app-fundamentals/data-binding/commanding.md).

## <a name="creating-a-swipe-container"></a>Creazione di un contenitore tramite passaggio del dito

Il `SwipeContainer` classe, che è illustrato nell'esempio di codice seguente, è una classe di riconoscimento scorrere rapidamente generalizzata che essere eseguito il wrapping intorno un [ `View` ](xref:Xamarin.Forms.View) per eseguire il riconoscimento di movimento scorrimento rapido:

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

Il `SwipeContainer` classe creato [ `SwipeGestureRecognizer` ](xref:Xamarin.Forms.SwipeGestureRecognizer) degli oggetti per tutte le direzioni di quattro scorrere rapidamente e associa `Swipe` gestori eventi. Questi gestori eventi richiamano il `Swipe` evento definito dal `SwipeContainer`.

L'esempio di codice XAML seguente mostra le `SwipeContainer` classe ritorno a capo una [ `BoxView` ](xref:Xamarin.Forms.BoxView):

```xaml
<ContentPage ...>
    <StackLayout>
        <local:SwipeContainer Swipe="OnSwiped" ...>
            <BoxView Color="Teal" ... />
        </local:SwipeContainer>
    </StackLayout>
</ContentPage>
```

Nell'esempio di codice riportato di seguito viene illustrato come la `SwipeContainer` esegue il wrapping di un [ `BoxView` ](xref:Xamarin.Forms.BoxView) in un C# pagina:

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

Quando la [ `BoxView` ](xref:Xamarin.Forms.BoxView) riceve un gesto di scorrimento rapido, il [ `Swiped` ](xref:Xamarin.Forms.SwipeGestureRecognizer.Swiped) evento nel [ `SwipeGestureRecognizer` ](xref:Xamarin.Forms.SwipeGestureRecognizer) viene attivato. Questa operazione viene gestita dal `SwipeContainer` (classe), che viene attivato il proprio `Swipe` evento. Ciò `Swipe` viene gestito l'evento nella pagina. Il [ `SwipedEventArgs` ](xref:Xamarin.Forms.SwipedEventArgs) possono quindi essere esaminati per determinare la direzione dello sfioramento, con la logica personalizzata risponde allo sfioramento in base alle esigenze.

## <a name="related-links"></a>Collegamenti correlati

- [Gesto di scorrimento rapido (esempio)](https://developer.xamarin.com/samples/xamarin-forms/WorkingWithGestures/SwipeGesture/)
- [GestureRecognizer](xref:Xamarin.Forms.GestureRecognizer)
- [SwipeGestureRecognizer](xref:Xamarin.Forms.SwipeGestureRecognizer)
