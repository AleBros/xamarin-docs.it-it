---
title: Aggiunta di un sistema di riconoscimento del movimento di scorrimento rapido
description: Questo articolo spiega come riconoscere un movimento di scorrimento rapido che si verifica in una vista.
ms.prod: xamarin
ms.assetid: 164976C2-1429-49FB-9EB6-621E2681C19B
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/14/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 03663803313c870a3361c6e1ffc85cf1f8999068
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/18/2020
ms.locfileid: "84137885"
---
# <a name="adding-a-swipe-gesture-recognizer"></a>Aggiunta di un sistema di riconoscimento del movimento di scorrimento rapido

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithgestures-swipegesture)

_Un gesto di scorrimento rapido si verifica quando un dito viene spostato sullo schermo in direzione orizzontale o verticale e viene spesso usato per avviare la navigazione nel contenuto. Gli esempi di codice in questo articolo sono tratti dall'esempio di [gesto di scorrimento](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithgestures-swipegesture) ._

Per fare in modo che [`View`](xref:Xamarin.Forms.View) riconosca un gesto di scorrimento, creare un' [`SwipeGestureRecognizer`](xref:Xamarin.Forms.SwipeGestureRecognizer) istanza, impostare la [`Direction`](xref:Xamarin.Forms.SwipeGestureRecognizer.Direction) proprietà su un [`SwipeDirection`](xref:Xamarin.Forms.SwipeDirection) valore di enumerazione ( `Left` ,, `Right` `Up` o `Down` ), impostare facoltativamente la [`Threshold`](xref:Xamarin.Forms.SwipeGestureRecognizer.Threshold) proprietà, gestire l' [`Swiped`](xref:Xamarin.Forms.SwipeGestureRecognizer.Swiped) evento e aggiungere il nuovo riconoscimento di movimento alla [`GestureRecognizers`](xref:Xamarin.Forms.View.GestureRecognizers) raccolta nella vista. Nell'esempio di codice riportato di seguito viene illustrato un oggetto `SwipeGestureRecognizer` associato a un oggetto [`BoxView`](xref:Xamarin.Forms.BoxView) :

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

La [`SwipeGestureRecognizer`](xref:Xamarin.Forms.SwipeGestureRecognizer) classe include anche una [`Threshold`](xref:Xamarin.Forms.SwipeGestureRecognizer.Threshold) proprietà, che può essere facoltativamente impostata su un `uint` valore che rappresenta la distanza minima di scorrimento che è necessario ottenere per poter riconoscere un swipe, in unità indipendenti dal dispositivo. Il valore predefinito di questa proprietà è 100, ovvero tutti gli scorrimenti rapidi inferiori a 100 unità indipendenti dal dispositivo verranno ignorati.

## <a name="recognizing-the-swipe-direction"></a>Riconoscimento della direzione di scorrimento

Negli esempi precedenti, la [`Direction`](xref:Xamarin.Forms.SwipedEventArgs.Direction) proprietà è impostata su Single un valore dell' [`SwipeDirection`](xref:Xamarin.Forms.SwipeDirection) enumerazione. Tuttavia, è anche possibile impostare questa proprietà su più valori dell' `SwipeDirection` enumerazione, in modo che l' [`Swiped`](xref:Xamarin.Forms.SwipeGestureRecognizer.Swiped) evento venga generato in risposta a un swipe in più di una direzione. Tuttavia, il vincolo è che un singolo è [`SwipeGestureRecognizer`](xref:Xamarin.Forms.SwipeGestureRecognizer) in grado di riconoscere solo i sfioramenti che si verificano sullo stesso asse. Di conseguenza, gli scorrimenti rapidi che si verificano sull'asse orizzontale possono essere riconosciuti impostando la proprietà `Direction` su `Left` e `Right`:

```xaml
<SwipeGestureRecognizer Direction="Left,Right" Swiped="OnSwiped"/>
```

Analogamente, gli scorrimenti che si verificano sull'asse verticale possono essere riconosciuti impostando la [`Direction`](xref:Xamarin.Forms.SwipedEventArgs.Direction) proprietà su `Up` e `Down` :

```csharp
var swipeGesture = new SwipeGestureRecognizer { Direction = SwipeDirection.Up | SwipeDirection.Down };
```

In alternativa, [`SwipeGestureRecognizer`](xref:Xamarin.Forms.SwipeGestureRecognizer) è possibile creare un per ciascuna direzione di scorrimento per riconoscere i scorrimenti a ogni direzione:

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
> Negli esempi precedenti, lo stesso gestore eventi risponde all' [`Swiped`](xref:Xamarin.Forms.SwipeGestureRecognizer.Swiped) attivazione dell'evento. Tuttavia, [`SwipeGestureRecognizer`](xref:Xamarin.Forms.SwipeGestureRecognizer) se necessario, ogni istanza può utilizzare un gestore eventi diverso.

## <a name="responding-to-the-swipe"></a>Risposta allo scorrimento rapido

[`Swiped`](xref:Xamarin.Forms.SwipeGestureRecognizer.Swiped)Nell'esempio seguente viene illustrato un gestore eventi per l'evento:

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

Il [`SwipedEventArgs`](xref:Xamarin.Forms.SwipedEventArgs) può essere esaminato per determinare la direzione del dito, con la logica personalizzata che risponde al swipe secondo le necessità. La direzione della striscia può essere ottenuta dalla [`Direction`](xref:Xamarin.Forms.SwipedEventArgs.Direction) proprietà degli argomenti dell'evento, che verrà impostata su uno dei valori dell' [`SwipeDirection`](xref:Xamarin.Forms.SwipeDirection) enumerazione. Inoltre, gli argomenti dell'evento hanno anche una [`Parameter`](xref:Xamarin.Forms.SwipedEventArgs.Parameter) proprietà che verrà impostata sul valore della [`CommandParameter`](xref:Xamarin.Forms.SwipeGestureRecognizer.CommandParameter) proprietà, se definito.

## <a name="using-commands"></a>Uso dei comandi

La [`SwipeGestureRecognizer`](xref:Xamarin.Forms.SwipeGestureRecognizer) classe include anche [`Command`](xref:Xamarin.Forms.SwipeGestureRecognizer.Command) le [`CommandParameter`](xref:Xamarin.Forms.SwipeGestureRecognizer.CommandParameter) proprietà e. Queste proprietà in genere vengono usate nelle applicazioni che usano il modello Model-View-ViewModel (MVVM). La `Command` proprietà definisce l' `ICommand` oggetto da richiamare quando viene riconosciuto un movimento di scorrimento rapido, con la `CommandParameter` proprietà che definisce un oggetto da passare all' `ICommand.` esempio di codice seguente illustra come associare la `Command` proprietà a un oggetto `ICommand` definito nel modello di visualizzazione la cui istanza è impostata come pagina [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) :

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

`SwipeCommand`è una proprietà di tipo `ICommand` definita nell'istanza del modello di visualizzazione impostata come pagina [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) . Quando viene riconosciuto un movimento di scorrimento rapido, viene eseguito il metodo `Execute` dell'oggetto `SwipeCommand`. L'argomento del `Execute` metodo è il valore della [`CommandParameter`](xref:Xamarin.Forms.SwipeGestureRecognizer.CommandParameter) Proprietà. Per altre informazioni sui comandi, vedere l'articolo sull'[interfaccia di comando](~/xamarin-forms/app-fundamentals/data-binding/commanding.md).

## <a name="creating-a-swipe-container"></a>Creazione di un contenitore dello scorrimento rapido

La `SwipeContainer` classe, illustrata nell'esempio di codice seguente, è una classe di riconoscimento swipe generalizzata che viene avvolta intorno a [`View`](xref:Xamarin.Forms.View) per eseguire il riconoscimento dei movimenti di scorrimento:

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

La `SwipeContainer` classe crea [`SwipeGestureRecognizer`](xref:Xamarin.Forms.SwipeGestureRecognizer) oggetti per tutte e quattro le direzioni di scorrimento e collega i `Swipe` gestori eventi. Questi gestori eventi richiamano l'evento `Swipe` definito da `SwipeContainer`.

Nell'esempio di codice XAML riportato di seguito viene illustrata la classe che include `SwipeContainer` un oggetto [`BoxView`](xref:Xamarin.Forms.BoxView) :

```xaml
<ContentPage ...>
    <StackLayout>
        <local:SwipeContainer Swipe="OnSwiped" ...>
            <BoxView Color="Teal" ... />
        </local:SwipeContainer>
    </StackLayout>
</ContentPage>
```

Nell'esempio di codice seguente viene illustrato come l'oggetto `SwipeContainer` esegue il wrapping di un oggetto [`BoxView`](xref:Xamarin.Forms.BoxView) in una pagina C#:

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

Quando [`BoxView`](xref:Xamarin.Forms.BoxView) riceve un gesto di scorrimento, [`Swiped`](xref:Xamarin.Forms.SwipeGestureRecognizer.Swiped) viene generato l'evento in [`SwipeGestureRecognizer`](xref:Xamarin.Forms.SwipeGestureRecognizer) . Questa operazione è gestita dalla classe `SwipeContainer` che attiva il proprio evento `Swipe`. L'evento `Swipe` viene gestito nella pagina. Il [`SwipedEventArgs`](xref:Xamarin.Forms.SwipedEventArgs) può quindi essere esaminato per determinare la direzione del dito, con la logica personalizzata che risponde al swipe secondo le necessità.

## <a name="related-links"></a>Collegamenti correlati

- [Swipe Gesture (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithgestures-swipegesture)
- [GestureRecognizer](xref:Xamarin.Forms.GestureRecognizer)
- [SwipeGestureRecognizer](xref:Xamarin.Forms.SwipeGestureRecognizer)
