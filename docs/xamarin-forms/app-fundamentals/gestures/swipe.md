---
title: Aggiunta di un sistema di riconoscimento del movimento di scorrimento rapido
description: Questo articolo spiega come riconoscere un movimento di scorrimento rapido che si verifica in una vista.
ms.prod: xamarin
ms.assetid: 164976C2-1429-49FB-9EB6-621E2681C19B
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/14/2018
ms.openlocfilehash: ae9b5eb5b768b50ddcbc199040074de855f220de
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2020
ms.locfileid: "68649452"
---
# <a name="adding-a-swipe-gesture-recognizer"></a>Aggiunta di un sistema di riconoscimento del movimento di scorrimento rapido

[![Scarica](~/media/shared/download.png) l'esempio Scarica l'esempioDownload Sample Download the sample](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithgestures-swipegesture)

_Un gesto di scorrimento rapido si verifica quando un dito viene spostato sullo schermo in direzione orizzontale o verticale e viene spesso utilizzato per avviare la navigazione nel contenuto. Gli esempi di codice in questo articolo sono tratti dall'esempio [di gesti di scorrimento](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithgestures-swipegesture) rapido._

Per rendere [`View`](xref:Xamarin.Forms.View) riconosciuto un movimento di scorrimento rapido, creare un'istanza, [`SwipeGestureRecognizer`](xref:Xamarin.Forms.SwipeGestureRecognizer) impostare la [`Direction`](xref:Xamarin.Forms.SwipeGestureRecognizer.Direction) proprietà su un [`SwipeDirection`](xref:Xamarin.Forms.SwipeDirection) valore di enumerazione (`Left`, `Right`, `Up`, o `Down`), impostare facoltativamente la [`Threshold`](xref:Xamarin.Forms.SwipeGestureRecognizer.Threshold) proprietà , gestire l'evento [`Swiped`](xref:Xamarin.Forms.SwipeGestureRecognizer.Swiped) e aggiungere il nuovo riconoscitore di movimento alla [`GestureRecognizers`](xref:Xamarin.Forms.View.GestureRecognizers) raccolta nella visualizzazione. Nell'esempio di `SwipeGestureRecognizer` codice riportato [`BoxView`](xref:Xamarin.Forms.BoxView)di seguito viene illustrato un elemento associato a :

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

La [`SwipeGestureRecognizer`](xref:Xamarin.Forms.SwipeGestureRecognizer) classe include [`Threshold`](xref:Xamarin.Forms.SwipeGestureRecognizer.Threshold) anche una proprietà, che `uint` può essere facoltativamente impostata su un valore che rappresenta la distanza minima di scorrimento che deve essere raggiunta affinché venga riconosciuto uno scorrimento rapido, in unità indipendenti dal dispositivo. Il valore predefinito di questa proprietà è 100, ovvero tutti gli scorrimenti rapidi inferiori a 100 unità indipendenti dal dispositivo verranno ignorati.

## <a name="recognizing-the-swipe-direction"></a>Riconoscimento della direzione di scorrimento

Negli esempi precedenti, [`Direction`](xref:Xamarin.Forms.SwipedEventArgs.Direction) la proprietà è impostata [`SwipeDirection`](xref:Xamarin.Forms.SwipeDirection) su un singolo valore dell'enumerazione. Tuttavia, è anche possibile impostare questa proprietà `SwipeDirection` su più [`Swiped`](xref:Xamarin.Forms.SwipeGestureRecognizer.Swiped) valori dall'enumerazione, in modo che l'evento viene generato in risposta a uno scorrimento rapido in più direzioni. Tuttavia, il vincolo [`SwipeGestureRecognizer`](xref:Xamarin.Forms.SwipeGestureRecognizer) è che un singolo può riconoscere solo le scorrimenti che si verificano sullo stesso asse. Di conseguenza, gli scorrimenti rapidi che si verificano sull'asse orizzontale possono essere riconosciuti impostando la proprietà `Direction` su `Left` e `Right`:

```xaml
<SwipeGestureRecognizer Direction="Left,Right" Swiped="OnSwiped"/>
```

Analogamente, i colpi di scorrimento che si [`Direction`](xref:Xamarin.Forms.SwipedEventArgs.Direction) verificano `Up` `Down`sull'asse verticale possono essere riconosciuti impostando la proprietà su e :

```csharp
var swipeGesture = new SwipeGestureRecognizer { Direction = SwipeDirection.Up | SwipeDirection.Down };
```

In alternativa, [`SwipeGestureRecognizer`](xref:Xamarin.Forms.SwipeGestureRecognizer) è possibile creare un per ogni direzione di scorrimento per riconoscere i colpi in ogni direzione:

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
> Negli esempi precedenti, lo stesso gestore [`Swiped`](xref:Xamarin.Forms.SwipeGestureRecognizer.Swiped) eventi risponde alla generazione dell'evento. Tuttavia, [`SwipeGestureRecognizer`](xref:Xamarin.Forms.SwipeGestureRecognizer) ogni istanza può usare un gestore eventi diverso, se necessario.

## <a name="responding-to-the-swipe"></a>Risposta allo scorrimento rapido

Nell'esempio seguente [`Swiped`](xref:Xamarin.Forms.SwipeGestureRecognizer.Swiped) viene illustrato un gestore eventi per l'evento:An event handler for the event is shown in the following example:

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

Il [`SwipedEventArgs`](xref:Xamarin.Forms.SwipedEventArgs) può essere esaminato per determinare la direzione dello scorrimento, con logica personalizzata che risponde allo scorrimento come richiesto. La direzione dello scorrimento può [`Direction`](xref:Xamarin.Forms.SwipedEventArgs.Direction) essere ottenuta dalla proprietà degli argomenti dell'evento, che verrà impostata su uno dei valori dell'enumerazione. [`SwipeDirection`](xref:Xamarin.Forms.SwipeDirection) Inoltre, gli argomenti dell'evento hanno anche una [`Parameter`](xref:Xamarin.Forms.SwipedEventArgs.Parameter) proprietà [`CommandParameter`](xref:Xamarin.Forms.SwipeGestureRecognizer.CommandParameter) che verrà impostata sul valore della proprietà, se definita.

## <a name="using-commands"></a>Uso dei comandi

La [`SwipeGestureRecognizer`](xref:Xamarin.Forms.SwipeGestureRecognizer) classe [`Command`](xref:Xamarin.Forms.SwipeGestureRecognizer.Command) include [`CommandParameter`](xref:Xamarin.Forms.SwipeGestureRecognizer.CommandParameter) anche le proprietà e . Queste proprietà in genere vengono usate nelle applicazioni che usano il modello Model-View-ViewModel (MVVM). La `Command` proprietà definisce l'oggetto `ICommand` da richiamare quando `CommandParameter` viene riconosciuto un movimento `ICommand.` di scorrimento rapido, con `Command` la `ICommand` proprietà che definisce un oggetto da [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext)passare all'esempio di codice seguente viene illustrato come associare la proprietà a un definito nel modello di visualizzazione la cui istanza è impostata come pagina :

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

`SwipeCommand`è una proprietà `ICommand` di tipo definita nell'istanza del [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext)modello di visualizzazione impostata come pagina . Quando viene riconosciuto un movimento di scorrimento rapido, viene eseguito il metodo `Execute` dell'oggetto `SwipeCommand`. L'argomento `Execute` del metodo è [`CommandParameter`](xref:Xamarin.Forms.SwipeGestureRecognizer.CommandParameter) il valore della proprietà. Per altre informazioni sui comandi, vedere l'articolo sull'[interfaccia di comando](~/xamarin-forms/app-fundamentals/data-binding/commanding.md).

## <a name="creating-a-swipe-container"></a>Creazione di un contenitore dello scorrimento rapido

La `SwipeContainer` classe, illustrata nell'esempio di codice seguente, è una classe [`View`](xref:Xamarin.Forms.View) di riconoscimento scorrimento rapido generalizzata di cui viene eseguito il wrapping intorno a una classe per eseguire il riconoscimento tramite tocco:

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

La `SwipeContainer` classe [`SwipeGestureRecognizer`](xref:Xamarin.Forms.SwipeGestureRecognizer) crea oggetti per tutte e `Swipe` quattro le direzioni di scorrimento e associa i gestori eventi. Questi gestori eventi richiamano l'evento `Swipe` definito da `SwipeContainer`.

Nell'esempio di codice `SwipeContainer` XAML [`BoxView`](xref:Xamarin.Forms.BoxView)seguente viene illustrata la classe che esegue il wrapping di un:

```xaml
<ContentPage ...>
    <StackLayout>
        <local:SwipeContainer Swipe="OnSwiped" ...>
            <BoxView Color="Teal" ... />
        </local:SwipeContainer>
    </StackLayout>
</ContentPage>
```

Nell'esempio di codice `SwipeContainer` seguente [`BoxView`](xref:Xamarin.Forms.BoxView) viene illustrato come eseguire il wrapping di un in una pagina di C :The following code example shows how the wraps a in a c'è page:

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

Quando [`BoxView`](xref:Xamarin.Forms.BoxView) l'oggetto riceve un [`Swiped`](xref:Xamarin.Forms.SwipeGestureRecognizer.Swiped) gesto [`SwipeGestureRecognizer`](xref:Xamarin.Forms.SwipeGestureRecognizer) di scorrimento rapido, viene generato l'evento nel . Questa operazione è gestita dalla classe `SwipeContainer` che attiva il proprio evento `Swipe`. L'evento `Swipe` viene gestito nella pagina. Il [`SwipedEventArgs`](xref:Xamarin.Forms.SwipedEventArgs) può quindi essere esaminato per determinare la direzione dello scorrimento, con logica personalizzata che risponde allo scorrimento come richiesto.

## <a name="related-links"></a>Collegamenti correlati

- [Swipe Gesture (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithgestures-swipegesture)
- [GestureRecognizer](xref:Xamarin.Forms.GestureRecognizer)
- [SwipeGestureRecognizer](xref:Xamarin.Forms.SwipeGestureRecognizer)
