---
title: " Xamarin.Forms ScrollView" Description: "questo articolo spiega come usare la Xamarin.Forms classe ScrollView per presentare i layout che non possono essere inseriti in una sola schermata e con contenuto che fa spazio per la tastiera".
ms. prod: ms. AssetID: ms. Technology: autore: ms. Author: ms. Date: No-loc:
- 'Xamarin.Forms'
- 'Xamarin.Essentials'

---

# <a name="xamarinforms-scrollview"></a>Xamarin.FormsScrollView

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-layout)

[`ScrollView`](xref:Xamarin.Forms.ScrollView)contiene layout e consente di scorrere Offscreen. `ScrollView`viene inoltre utilizzato per consentire alle visualizzazioni di spostarsi automaticamente sulla parte visibile dello schermo quando viene visualizzata la tastiera.

[![](scroll-view-images/layouts-sml.png "Xamarin.Forms Layouts")](scroll-view-images/layouts.png#lightbox "Xamarin.Forms Layouts")

## <a name="purpose"></a>Scopo

[`ScrollView`](xref:Xamarin.Forms.ScrollView)può essere usato per garantire che le visualizzazioni più grandi vengano visualizzate correttamente sui telefoni più piccoli. Ad esempio, un layout che funziona su un iPhone 6s può essere ritagliato in un iPhone 4S. Se si utilizza un oggetto `ScrollView` , le parti ritagliate del layout verranno visualizzate nella schermata più piccola.

## <a name="usage"></a>Utilizzo

> [!NOTE]
> [`ScrollView`](xref:Xamarin.Forms.ScrollView)gli oggetti non devono essere annidati. Inoltre, `ScrollView` i non devono essere annidati con altri controlli che forniscono lo scorrimento, ad esempio `ListView` e `WebView` .

[`ScrollView`](xref:Xamarin.Forms.ScrollView)espone una `Content` proprietà, che può essere impostata su una singola visualizzazione o layout. Si consideri questo esempio di un layout con un boxView di grandi dimensioni, seguito da un `Entry` :

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
public class ScrollingDemoCode : ContentPage
{
    public ScrollingDemoCode()
    {
        StackLayout stackLayout = new StackLayout();
        stackLayout.Children.Add(new BoxView { BackgroundColor = Color.Red, HeightRequest = 600, WidthRequest = 150 });
        stackLayout.Children.Add(new Entry());
        ScrollView scrollView = new ScrollView();
        scrollView.Content = stackLayout;
        Content = scrollView;
    }
}
```

Prima di scorrere verso il basso, solo il `BoxView` è visibile:

![](scroll-view-images/scroll-start.png "BoxView in ScrollView")

Si noti che quando l'utente inizia a immettere il testo in `Entry` , la visualizzazione scorre per mantenerla visibile sullo schermo:

![](scroll-view-images/scroll-end.png "Entry in ScrollView")

## <a name="properties"></a>Proprietà

[`ScrollView`](xref:Xamarin.Forms.ScrollView)definisce le proprietà seguenti:

- [`ContentSize`](xref:Xamarin.Forms.ScrollView.ContentSizeProperty)Ottiene un [`Size`](xref:Xamarin.Forms.Size) valore che rappresenta le dimensioni del contenuto.
- [`Orientation`](xref:Xamarin.Forms.ScrollView.OrientationProperty)Ottiene o imposta un [`ScrollOrientation`](xref:Xamarin.Forms.ScrollOrientation) valore di enumerazione che rappresenta la direzione di scorrimento di `ScrollView` .
- [`ScrollX`](xref:Xamarin.Forms.ScrollView.ScrollXProperty)Ottiene un oggetto `double` che rappresenta la posizione di scorrimento X corrente.
- [`ScrollY`](xref:Xamarin.Forms.ScrollView.ScrollYProperty)Ottiene un oggetto `double` che rappresenta la posizione di scorrimento Y corrente.
- [`HorizontalScrollBarVisibility`](xref:Xamarin.Forms.ScrollView.HorizontalScrollBarVisibilityProperty)Ottiene o imposta un [`ScrollBarVisibility`](xref:Xamarin.Forms.ScrollBarVisibility) valore che rappresenta quando la barra di scorrimento orizzontale è visibile.
- [`VerticalScrollBarVisibility`](xref:Xamarin.Forms.ScrollView.VerticalScrollBarVisibilityProperty)Ottiene o imposta un [`ScrollBarVisibility`](xref:Xamarin.Forms.ScrollBarVisibility) valore che rappresenta quando la barra di scorrimento verticale è visibile.

> [!NOTE]
> Lo scorrimento può essere disabilitato impostando la [`Orientation`](xref:Xamarin.Forms.ScrollView.OrientationProperty) proprietà su `Neither` .

## <a name="methods"></a>Metodi

[`ScrollView`](xref:Xamarin.Forms.ScrollView)fornisce un `ScrollToAsync` metodo che può essere utilizzato per scorrere la visualizzazione utilizzando le coordinate o specificando una visualizzazione specifica che deve essere resa visibile.

Quando si usano le coordinate, specificare le `x` `y` coordinate e, insieme a un valore booleano che indica se è necessario animare lo scorrimento:

```csharp
scroll.ScrollToAsync(0, 150, true); //scrolls so that the position at 150px from the top is visible

scroll.ScrollToAsync(label, ScrollToPosition.Start, true); //scrolls so that the label is at the start of the list
```

> [!IMPORTANT]
> `ScrollToAsync`Se la [`ScrollView.Orientation`](xref:Xamarin.Forms.ScrollView.OrientationProperty) proprietà è impostata su, il metodo non comporterà lo scorrimento `Neither` .

Quando si scorre a un particolare elemento, l' `ScrollToPosition` enumerazione specifica il punto nella visualizzazione in cui verrà visualizzato l'elemento:

- Al **centro** &ndash; scorre l'elemento al centro della parte visibile della visualizzazione.
- **Fine** &ndash; scorre l'elemento alla fine della parte visibile della visualizzazione.
- **MakeVisible** &ndash; scorre l'elemento in modo che sia visibile nella visualizzazione.
- **Inizio** &ndash; scorre l'elemento all'inizio della parte visibile della visualizzazione.

La `IsAnimated` proprietà specifica il modo in cui verrà eseguito lo scorrimento della visualizzazione. Quando è impostato su `true` , verrà utilizzata un'animazione uniforme, anziché lo stato di visualizzazione immediata del contenuto.

## <a name="events"></a>Eventi

[`ScrollView`](xref:Xamarin.Forms.ScrollView)definisce solo un evento, `Scrolled` . `Scrolled`viene generato al termine dello scorrimento della visualizzazione. Il gestore eventi per `Scrolled` accetta `ScrolledEventArgs` , che dispone delle `ScrollX` `ScrollY` proprietà e. Di seguito viene illustrato come aggiornare un'etichetta con la posizione di scorrimento corrente di un oggetto `ScrollView` :

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
