---
title: Novell. Forms ScrollView
description: Questo articolo illustra come usare la classe Novell. Forms ScrollView per presentare layout che non possono essere inseriti in una sola schermata e con contenuto che fa spazio alla tastiera.
ms.prod: xamarin
ms.assetid: 7B542872-B3D1-49B3-B15E-0E98F53C1F6E
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/17/2019
ms.openlocfilehash: 8d523c6da6ca7feaf6894123822f789f37455865
ms.sourcegitcommit: 21d8be9571a2fa89fb7d8ff0787ff4f957de0985
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/21/2019
ms.locfileid: "72696862"
---
# <a name="xamarinforms-scrollview"></a>Novell. Forms ScrollView

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-layout)

[`ScrollView`](xref:Xamarin.Forms.ScrollView) contiene layout e consente di scorrere Offscreen. `ScrollView` viene inoltre utilizzato per consentire alle visualizzazioni di spostarsi automaticamente sulla parte visibile dello schermo quando viene visualizzata la tastiera.

[![](scroll-view-images/layouts-sml.png "Xamarin.Forms Layouts")](scroll-view-images/layouts.png#lightbox "Xamarin.Forms Layouts")

## <a name="purpose"></a>Scopo

è possibile usare [`ScrollView`](xref:Xamarin.Forms.ScrollView) per assicurarsi che le visualizzazioni più grandi vengano visualizzate correttamente sui telefoni più piccoli. Ad esempio, un layout che funziona su un iPhone 6s può essere ritagliato in un iPhone 4S. L'utilizzo di un `ScrollView` consentirebbe la visualizzazione delle parti ritagliate del layout sulla schermata più piccola.

## <a name="usage"></a>Utilizzo

> [!NOTE]
> gli oggetti [`ScrollView`](xref:Xamarin.Forms.ScrollView) non devono essere annidati. Inoltre, `ScrollView`s non deve essere annidato con altri controlli che forniscono lo scorrimento, ad esempio `ListView` e `WebView`.

[`ScrollView`](xref:Xamarin.Forms.ScrollView) espone una proprietà `Content`, che può essere impostata su una singola visualizzazione o layout. Si consideri questo esempio di un layout con un boxView di grandi dimensioni, seguito da un `Entry`:

```xaml
<ContentPage.Content>
    <ScrollView>
        <StackLayout>
            <BoxView BackgroundColor="Red" HeightRequest="600" WidthRequest="150" />
            <Entry />
        </StackLayout>
    </ScrollView>
</ContentPage.Content>
```

In C#:

```csharp
var scroll = new ScrollView();
Content = scroll;
var stack = new StackLayout();
stack.Children.Add(new BoxView { BackgroundColor = Color.Red,    HeightRequest = 600, WidthRequest = 600 });
stack.Children.Add(new Entry());
```

Prima che l'utente Scorri verso il basso, solo il `BoxView` è visibile:

![](scroll-view-images/scroll-start.png "BoxView in ScrollView")

Si noti che quando l'utente inizia a immettere il testo nella `Entry`, la visualizzazione scorre per mantenerla visibile sullo schermo:

![](scroll-view-images/scroll-end.png "Entry in ScrollView")

## <a name="properties"></a>Proprietà

[`ScrollView`](xref:Xamarin.Forms.ScrollView) definisce le proprietà seguenti:

- [`ContentSize`](xref:Xamarin.Forms.ScrollView.ContentSizeProperty) ottiene un valore [`Size`](xref:Xamarin.Forms.Size) che rappresenta le dimensioni del contenuto.
- [`Orientation`](xref:Xamarin.Forms.ScrollView.OrientationProperty) Ottiene o imposta un valore dell'enumerazione [`ScrollOrientation`](xref:Xamarin.Forms.ScrollOrientation) che rappresenta la direzione di scorrimento della `ScrollView`.
- [`ScrollX`](xref:Xamarin.Forms.ScrollView.ScrollXProperty) ottiene un `double` che rappresenta la posizione di scorrimento X corrente.
- [`ScrollY`](xref:Xamarin.Forms.ScrollView.ScrollYProperty) ottiene un `double` che rappresenta la posizione di scorrimento Y corrente.
- [`HorizontalScrollBarVisibility`](xref:Xamarin.Forms.ScrollView.HorizontalScrollBarVisibilityProperty) Ottiene o imposta un valore [`ScrollBarVisibility`](xref:Xamarin.Forms.ScrollBarVisibility) che rappresenta quando la barra di scorrimento orizzontale è visibile.
- [`VerticalScrollBarVisibility`](xref:Xamarin.Forms.ScrollView.VerticalScrollBarVisibilityProperty) Ottiene o imposta un valore [`ScrollBarVisibility`](xref:Xamarin.Forms.ScrollBarVisibility) che rappresenta quando la barra di scorrimento verticale è visibile.

> [!NOTE]
> Lo scorrimento può essere disabilitato impostando la proprietà [`Orientation`](xref:Xamarin.Forms.ScrollView.OrientationProperty) su `Neither`.

## <a name="methods"></a>Metodi

[`ScrollView`](xref:Xamarin.Forms.ScrollView) fornisce un metodo `ScrollToAsync`, che può essere usato per scorrere la visualizzazione usando le coordinate o specificando una visualizzazione specifica che deve essere resa visibile.

Quando si usano le coordinate, specificare le coordinate `x` e `y`, insieme a un valore booleano che indica se è necessario animare lo scorrimento:

```csharp
scroll.ScrollToAsync(0, 150, true); //scrolls so that the position at 150px from the top is visible

scroll.ScrollToAsync(label, ScrollToPosition.Start, true); //scrolls so that the label is at the start of the list
```

> [!IMPORTANT]
> Il metodo `ScrollToAsync` non comporterà lo scorrimento quando la proprietà [`ScrollView.Orientation`](xref:Xamarin.Forms.ScrollView.OrientationProperty) è impostata su `Neither`.

Quando si scorre a un particolare elemento, l'enumerazione `ScrollToPosition` specifica la posizione in cui verrà visualizzato l'elemento:

- **Center** &ndash; scorre l'elemento al centro della parte visibile della visualizzazione.
- **End** &ndash; scorre l'elemento alla fine della parte visibile della visualizzazione.
- **MakeVisible** &ndash; scorre l'elemento in modo che sia visibile nella visualizzazione.
- **Start** &ndash; scorre l'elemento all'inizio della parte visibile della visualizzazione.

La proprietà `IsAnimated` specifica come verrà eseguito lo scorrimento della visualizzazione. Quando è impostato su `true`, viene utilizzata un'animazione uniforme, anziché lo stato di visualizzazione immediata del contenuto.

## <a name="events"></a>eventi

[`ScrollView`](xref:Xamarin.Forms.ScrollView) definisce un solo evento, `Scrolled`. `Scrolled` viene generato al termine dello scorrimento della visualizzazione. Il gestore eventi per `Scrolled` accetta `ScrolledEventArgs`, che dispone delle proprietà `ScrollX` e `ScrollY`. Di seguito viene illustrato come aggiornare un'etichetta con la posizione di scorrimento corrente di un `ScrollView`:

```csharp
Label label = new Label { Text = "Position: " };
ScrollView scroll = new ScrollView();
scroll.Scrolled += (object sender, ScrolledEventArgs e) => {
    label.Text = "Position: " + e.ScrollX + " x " + e.ScrollY;
};
```

Si noti che le posizioni di scorrimento possono essere negative, a causa dell'effetto rimbalzo durante lo scorrimento alla fine di un elenco.

## <a name="related-links"></a>Collegamenti correlati

- [Layout (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-layout)
- [Esempio di BusinessTumble (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-businesstumble)
