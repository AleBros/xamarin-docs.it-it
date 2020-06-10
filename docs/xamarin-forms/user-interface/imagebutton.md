---
title: " Xamarin.Forms ImageButton" Description: "ImageButton Visualizza un'immagine e risponde a un tocco o a un clic che indirizza un'applicazione per eseguire un'attività specifica".
ms. prod: Novell MS. AssetID: B5906AB6-3F79-4FCB-8C78-1F0AF18AB39E ms. Technology: Novell-Forms Author: davidbritch ms. Author: dabritch ms. Date: 12/04/2019 no-loc: [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="xamarinforms-imagebutton"></a>Xamarin.FormsImageButton

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/formsgallery)

_Il ImageButton Visualizza un'immagine e risponde a una scelta o a un clic che indirizza un'applicazione per eseguire un'attività specifica._

La visualizzazione `ImageButton` combina la [`Button`](xref:Xamarin.Forms.Button) visualizzazione e la [`Image`](xref:Xamarin.Forms.Image) visualizzazione per creare un pulsante il cui contenuto è un'immagine. L'utente preme con un `ImageButton` dito o fa clic su di esso con il mouse per indirizzare l'applicazione in modo da eseguire un'attività specifica. Tuttavia, a differenza della `Button` vista, la `ImageButton` vista non ha alcun concetto di testo e aspetto del testo.

> [!NOTE]
> Mentre la [`Button`](xref:Xamarin.Forms.Button) vista definisce una [`Image`](xref:Xamarin.Forms.Button.Image) proprietà, che consente di visualizzare un'immagine in `Button` , questa proprietà deve essere utilizzata quando si visualizza una piccola icona accanto al `Button` testo.

Gli esempi di codice in questa guida sono tratti dall' [esempio FormsGallery](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/formsgallery).

## <a name="setting-the-image-source"></a>Impostazione dell'origine dell'immagine

`ImageButton`definisce una `Source` proprietà che deve essere impostata sull'immagine da visualizzare nel pulsante, con l'origine dell'immagine che è un file, un URI, una risorsa o un flusso. Per ulteriori informazioni sul caricamento di immagini da origini diverse, vedere [Immagini Xamarin.Forms in ](images.md).

Nell'esempio seguente viene illustrato come creare un'istanza di `ImageButton` in XAML:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="FormsGallery.XamlExamples.ImageButtonDemoPage"
             Title="ImageButton Demo">
    <StackLayout>
        <Label Text="ImageButton"
               FontSize="50"
               FontAttributes="Bold"
               HorizontalOptions="Center" />

       <ImageButton Source="XamarinLogo.png"
                    HorizontalOptions="Center"
                    VerticalOptions="CenterAndExpand" />
    </StackLayout>
</ContentPage>
```

La `Source` proprietà specifica l'immagine visualizzata nell'oggetto `ImageButton` . In questo esempio è impostato su un file locale che verrà caricato da ogni progetto di piattaforma, ottenendo gli screenshot seguenti:

[![ImageButton di base](imagebutton-images/BasicImageButton.png "ImageButton di base")](imagebutton-images/BasicImageButton-Large.png#lightbox "ImageButton di base")

Per impostazione predefinita, `ImageButton` è rettangolare, ma è possibile assegnargli angoli arrotondati usando la `CornerRadius` Proprietà. Per ulteriori informazioni sull' `ImageButton` aspetto, vedere [ImageButton Appearance](#imagebutton-appearance).

> [!NOTE]
> Mentre un `ImageButton` può caricare un GIF animato, verrà visualizzato solo il primo frame del GIF.

Nell'esempio seguente viene illustrato come creare una pagina equivalente dal punto di vista funzionale all'esempio XAML precedente, ma interamente in C#:

```csharp
public class ImageButtonDemoPage : ContentPage
{
    public ImageButtonDemoPage()
    {
        Label header = new Label
        {
            Text = "ImageButton",
            FontSize = 50,
            FontAttributes = FontAttributes.Bold,
            HorizontalOptions = LayoutOptions.Center
        };

        ImageButton imageButton = new ImageButton
        {
            Source = "XamarinLogo.png",
            HorizontalOptions = LayoutOptions.Center,
            VerticalOptions = LayoutOptions.CenterAndExpand
        };

        // Build the page.
        Title = "ImageButton Demo";
        Content = new StackLayout
        {
            Children = { header, imageButton }
        };
    }
}
```

## <a name="handling-imagebutton-clicks"></a>Gestione dei clic di ImageButton

`ImageButton`definisce un `Clicked` evento che viene generato quando l'utente tocca l'oggetto `ImageButton` con un dito o un puntatore del mouse. L'evento viene generato quando il dito o il pulsante del mouse viene rilasciato dall'area di `ImageButton` . La `ImageButton` proprietà deve essere `IsEnabled` impostata su `true` per rispondere ai tocchi.

Nell'esempio seguente viene illustrato come creare un'istanza di `ImageButton` in XAML e gestirne l' `Clicked` evento:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="FormsGallery.XamlExamples.ImageButtonDemoPage"
             Title="ImageButton Demo">
    <StackLayout>
        <Label Text="ImageButton"
               FontSize="50"
               FontAttributes="Bold"
               HorizontalOptions="Center" />

       <ImageButton Source="XamarinLogo.png"
                    HorizontalOptions="Center"
                    VerticalOptions="CenterAndExpand"
                    Clicked="OnImageButtonClicked" />

        <Label x:Name="label"
               Text="0 ImageButton clicks"
               FontSize="Large"
               HorizontalOptions="Center"
               VerticalOptions="CenterAndExpand" />
    </StackLayout>
</ContentPage>
```

L' `Clicked` evento è impostato su un gestore eventi denominato `OnImageButtonClicked` che si trova nel file code-behind:

```csharp
public partial class ImageButtonDemoPage : ContentPage
{
    int clickTotal;

    public ImageButtonDemoPage()
    {
        InitializeComponent();
    }

    void OnImageButtonClicked(object sender, EventArgs e)
    {
        clickTotal += 1;
        label.Text = $"{clickTotal} ImageButton click{(clickTotal == 1 ? "" : "s")}";
    }
}
```

Quando si tocca `ImageButton` viene eseguito il metodo `OnImageButtonClicked`. L' `sender` argomento è `ImageButton` responsabile di questo evento. È possibile usarlo per accedere all' `ImageButton` oggetto o per distinguere tra più `ImageButton` oggetti che condividono lo stesso `Clicked` evento.

Questo particolare `Clicked` gestore incrementa un contatore e visualizza il valore del contatore in un [`Label`](xref:Xamarin.Forms.Label) :

[![Pulsante ImageButton di base](imagebutton-images/ImageButton.png "Pulsante ImageButton di base")](imagebutton-images/ImageButton-Large.png#lightbox "Pulsante ImageButton di base")

Nell'esempio seguente viene illustrato come creare una pagina equivalente dal punto di vista funzionale all'esempio XAML precedente, ma interamente in C#:

```csharp
public class ImageButtonDemoPage : ContentPage
{
    Label label;
    int clickTotal = 0;

    public ImageButtonDemoPage()
    {
        Label header = new Label
        {
            Text = "ImageButton",
            FontSize = 50,
            FontAttributes = FontAttributes.Bold,
            HorizontalOptions = LayoutOptions.Center
        };

        ImageButton imageButton = new ImageButton
        {
            Source = "XamarinLogo.png",
            HorizontalOptions = LayoutOptions.Center,
            VerticalOptions = LayoutOptions.CenterAndExpand
        };
        imageButton.Clicked += OnImageButtonClicked;

        label = new Label
        {
            Text = "0 ImageButton clicks",
            FontSize = Device.GetNamedSize(NamedSize.Large, typeof(Label)),
            HorizontalOptions = LayoutOptions.Center,
            VerticalOptions = LayoutOptions.CenterAndExpand
        };

        // Build the page.
        Title = "ImageButton Demo";
        Content = new StackLayout
        {
            Children =
            {
                header,
                imageButton,
                label
            }
        };
    }

    void OnImageButtonClicked(object sender, EventArgs e)
    {
        clickTotal += 1;
        label.Text = $"{clickTotal} ImageButton click{(clickTotal == 1 ? "" : "s")}";
    }
}
```

## <a name="disabling-the-imagebutton"></a>Disabilitazione di ImageButton

A volte un'applicazione si trova in uno stato specifico in cui un particolare `ImageButton` clic non è un'operazione valida. In questi casi, l'oggetto `ImageButton` deve essere disabilitato impostando la relativa `IsEnabled` proprietà su `false` .

## <a name="using-the-command-interface"></a>Utilizzo dell'interfaccia di comando

È possibile che un'applicazione risponda ai `ImageButton` rubinetti senza gestire l' `Clicked` evento. `ImageButton`Implementa un meccanismo di notifica alternativo denominato _comando_ o interfaccia di _comando_ . È costituito da due proprietà:

- `Command`di tipo [`ICommand`](xref:System.Windows.Input.ICommand) , un'interfaccia definita nello [`System.Windows.Input`](xref:System.Windows.Input) spazio dei nomi.
- `CommandParameter`Proprietà di tipo [`Object`](xref:System.Object) .

Questo approccio è appropriato in relazione all'associazione dati e, in particolare, quando si implementa l'architettura MVC (Model-View-ViewModel).

Per ulteriori informazioni sull'utilizzo dell'interfaccia di comando, vedere [utilizzo dell'interfaccia del comando](button.md#using-the-command-interface) nella guida del [pulsante](button.md) .

## <a name="pressing-and-releasing-the-imagebutton"></a>Pressione e rilascio del ImageButton

Oltre all'evento `Clicked`, `ImageButton` definisce anche gli eventi `Pressed` e `Released`. L' `Pressed` evento si verifica quando si preme un dito su un oggetto `ImageButton` o si preme un pulsante del mouse con il puntatore posizionato su `ImageButton` . L' `Released` evento si verifica quando viene rilasciato il dito o il pulsante del mouse. In genere, l' `Clicked` evento viene generato contemporaneamente all' `Released` evento, ma se il puntatore del dito o del mouse si allontana dalla superficie di `ImageButton` prima del rilascio, l' `Clicked` evento potrebbe non verificarsi.

Per ulteriori informazioni su questi eventi, vedere [premere e rilasciare il pulsante](button.md#pressing-and-releasing-the-button) nella guida del [pulsante](button.md) .

## <a name="imagebutton-appearance"></a>Aspetto di ImageButton

Oltre alle proprietà che `ImageButton` ereditano dalla [`View`](xref:Xamarin.Forms.View) classe, `ImageButton` definisce anche diverse proprietà che influiscono sull'aspetto:

- `Aspect`è il modo in cui l'immagine verrà ridimensionata in base all'area di visualizzazione.
- `BorderColor`colore di un'area che circonda l'oggetto `ImageButton` .
- `BorderWidth`larghezza del bordo.
- `CornerRadius`è il raggio dell'angolo dell'oggetto `ImageButton` .

La `Aspect` proprietà può essere impostata su uno dei membri dell' [`Aspect`](xref:Xamarin.Forms.Aspect) enumerazione:

- [`Fill`](xref:Xamarin.Forms.Aspect.Fill): estende l'immagine in modo che riempia completamente e esattamente `ImageButton` . Questa operazione può comportare la distorsione dell'immagine.
- [`AspectFill`](xref:Xamarin.Forms.Aspect.AspectFill): Ritaglia l'immagine in modo da riempire l'oggetto `ImageButton` mantenendo le proporzioni.
- [`AspectFit`](xref:Xamarin.Forms.Aspect.AspectFit)-Letterbox l'immagine, se necessario, in modo che l'intera immagine si inserisce nell'oggetto `ImageButton` , con uno spazio vuoto aggiunto alla parte superiore/inferiore o ai lati, a seconda che l'immagine sia di grandi dimensioni o di altezza. Si tratta del valore predefinito dell' [`Aspect`](xref:Xamarin.Forms.Aspect) enumerazione.

> [!NOTE]
> La `ImageButton` classe dispone inoltre [`Margin`](xref:Xamarin.Forms.View.Margin) `Padding` di proprietà e che controllano il comportamento del layout di `ImageButton` . Per altre informazioni, vedere l'articolo sulle proprietà [Margin e Padding](~/xamarin-forms/user-interface/layouts/margin-and-padding.md).

## <a name="imagebutton-visual-states"></a>Stati di visualizzazione ImageButton

`ImageButton`dispone di un oggetto `Pressed` [`VisualState`](xref:Xamarin.Forms.VisualState) che può essere utilizzato per avviare una modifica visiva a `ImageButton` quando viene premuto dall'utente, a condizione che sia abilitato.

Nell'esempio di codice XAML riportato di seguito viene illustrato come definire uno stato di visualizzazione per lo `Pressed` stato:

```xaml
<ImageButton Source="XamarinLogo.png"
             ...>
    <VisualStateManager.VisualStateGroups>
        <VisualStateGroup x:Name="CommonStates">
            <VisualState x:Name="Normal">
                <VisualState.Setters>
                    <Setter Property="Scale"
                            Value="1" />
                </VisualState.Setters>
            </VisualState>

            <VisualState x:Name="Pressed">
                <VisualState.Setters>
                    <Setter Property="Scale"
                            Value="0.8" />
                </VisualState.Setters>
            </VisualState>

        </VisualStateGroup>
    </VisualStateManager.VisualStateGroups>
</ImageButton>
```

`Pressed` [`VisualState`](xref:Xamarin.Forms.VisualState) Specifica che `ImageButton` , quando viene premuto, la relativa [`Scale`](xref:Xamarin.Forms.VisualElement.Scale) proprietà verrà modificata rispetto al valore predefinito da 1 a 0,8. `Normal` `VisualState` Specifica che quando `ImageButton` è in uno stato normale, la relativa `Scale` proprietà sarà impostata su 1. Pertanto, l'effetto complessivo è che, quando `ImageButton` viene premuto, viene ridimensionato in modo da essere leggermente più piccolo e, quando `ImageButton` viene rilasciato, viene ridimensionato in base alle dimensioni predefinite.

Per ulteriori informazioni sugli stati visivi, vedere la pagina relativa a [ Xamarin.Forms Visual State Manager](~/xamarin-forms/user-interface/visual-state-manager.md).

## <a name="related-links"></a>Collegamenti correlati

- [Esempio FormsGallery](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/formsgallery)
