---
title: Xamarin. Forms ImageButton
description: Elemento ImageButton Visualizza un'immagine e risponde a un tocco o clic che indirizza un'applicazione per eseguire una determinata attività.
ms.prod: xamarin
ms.assetid: B5906AB6-3F79-4FCB-8C78-1F0AF18AB39E
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/04/2019
ms.openlocfilehash: 7c6647a0299b5ece3caaaa1d322ec1a0efac3557
ms.sourcegitcommit: eca3b01098dba004d367292c8b0d74b58c4e1206
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79304135"
---
# <a name="xamarinforms-imagebutton"></a>Xamarin. Forms ImageButton

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/formsgallery)

_Il ImageButton Visualizza un'immagine e risponde a una scelta o a un clic che indirizza un'applicazione per eseguire un'attività specifica._

La visualizzazione `ImageButton` combina la visualizzazione [`Button`](xref:Xamarin.Forms.Button) e [`Image`](xref:Xamarin.Forms.Image) visualizzazione per creare un pulsante il cui contenuto è un'immagine. L'utente preme il `ImageButton` con un dito o fa clic su di esso con il mouse per indirizzare l'applicazione in modo da eseguire un'attività specifica. Tuttavia, a differenza della vista `Button`, la visualizzazione `ImageButton` non ha alcun concetto di testo e aspetto del testo.

> [!NOTE]
> Mentre la vista [`Button`](xref:Xamarin.Forms.Button) definisce una proprietà [`Image`](xref:Xamarin.Forms.Button.Image) , che consente di visualizzare un'immagine nel `Button`, questa proprietà deve essere utilizzata quando si visualizza un'icona di piccole dimensioni accanto al testo `Button`.

Gli esempi di codice in questa guida sono tratti dall' [esempio FormsGallery](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/formsgallery).

## <a name="setting-the-image-source"></a>Impostare l'origine dell'immagine

`ImageButton` definisce una proprietà `Source` da impostare sull'immagine da visualizzare nel pulsante, con l'origine dell'immagine che è un file, un URI, una risorsa o un flusso. Per altre informazioni sul caricamento di immagini da origini diverse, vedere [Immagini in Novell. Forms](images.md).

Nell'esempio seguente viene illustrato come creare un'istanza di un `ImageButton` in XAML:

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

La proprietà `Source` specifica l'immagine che viene visualizzata nella `ImageButton`. In questo esempio il valore è impostato in un file locale che verrà caricato da ogni progetto della piattaforma, generando gli screenshot seguenti:

[![ImageButton di base](imagebutton-images/BasicImageButton.png "ImageButton di base")](imagebutton-images/BasicImageButton-Large.png#lightbox "ImageButton di base")

Per impostazione predefinita, il `ImageButton` è rettangolare, ma è possibile assegnargli angoli arrotondati usando la proprietà `CornerRadius`. Per ulteriori informazioni sull'aspetto di `ImageButton`, vedere [ImageButton Appearance](#imagebutton-appearance).

> [!NOTE]
> Sebbene un `ImageButton` possa caricare un GIF animato, visualizzerà solo il primo frame del GIF.

Nell'esempio seguente viene illustrato come creare una pagina che è funzionalmente equivalente all'esempio XAML precedente, ma interamente nel C#:

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

## <a name="handling-imagebutton-clicks"></a>La gestione ImageButton fa clic su

`ImageButton` definisce un evento `Clicked` che viene generato quando l'utente tocca il `ImageButton` con un dito o un puntatore del mouse. L'evento viene generato quando il pulsante del dito o del mouse viene rilasciato dalla superficie del `ImageButton`. Il `ImageButton` deve avere la proprietà `IsEnabled` impostata su `true` per rispondere ai tocchi.

Nell'esempio seguente viene illustrato come creare un'istanza di un `ImageButton` in XAML e gestirne l'evento `Clicked`:

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

L'evento `Clicked` viene impostato su un gestore eventi denominato `OnImageButtonClicked` che si trova nel file code-behind:

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

Quando viene toccato il `ImageButton`, viene eseguito il metodo `OnImageButtonClicked`. L'argomento `sender` è il `ImageButton` responsabile di questo evento. È possibile usarlo per accedere all'oggetto `ImageButton` o per distinguere tra più oggetti `ImageButton` che condividono lo stesso evento di `Clicked`.

Questo particolare gestore di `Clicked` incrementa un contatore e visualizza il valore del contatore in una [`Label`](xref:Xamarin.Forms.Label):

[![Pulsante ImageButton di base](imagebutton-images/ImageButton.png "Pulsante ImageButton di base")](imagebutton-images/ImageButton-Large.png#lightbox "Pulsante ImageButton di base")

Nell'esempio seguente viene illustrato come creare una pagina che è funzionalmente equivalente all'esempio XAML precedente, ma interamente nel C#:

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

## <a name="disabling-the-imagebutton"></a>La disabilitazione di ImageButton

A volte un'applicazione si trova in uno stato specifico in cui un particolare `ImageButton` clic non è un'operazione valida. In questi casi, è necessario disabilitare il `ImageButton` impostando la relativa proprietà `IsEnabled` su `false`.

## <a name="using-the-command-interface"></a>Tramite l'interfaccia di comando

È possibile che un'applicazione risponda ai rubinetti `ImageButton` senza gestire l'evento di `Clicked`. Il `ImageButton` implementa un meccanismo di notifica alternativo denominato _comando_ o interfaccia di _comando_ . Questo è costituito da due proprietà:

- `Command` di tipo [`ICommand`](xref:System.Windows.Input.ICommand), un'interfaccia definita nello spazio dei nomi [`System.Windows.Input`](xref:System.Windows.Input) .
- `CommandParameter` proprietà di tipo [`Object`](xref:System.Object).

Questo approccio è adatto in relazione al data binding e in particolare quando si implementa l'architettura Model-View-ViewModel (MVVM).

Per ulteriori informazioni sull'utilizzo dell'interfaccia di comando, vedere [utilizzo dell'interfaccia del comando](button.md#using-the-command-interface) nella guida del [pulsante](button.md) .

## <a name="pressing-and-releasing-the-imagebutton"></a>Premere e rilasciare il ImageButton

Oltre all'evento `Clicked`, `ImageButton` definisce anche gli eventi di `Pressed` e `Released`. L'evento `Pressed` si verifica quando si preme un dito su un `ImageButton`oppure viene premuto un pulsante del mouse con il puntatore posizionato sul `ImageButton`. L'evento `Released` si verifica quando viene rilasciato il dito o il pulsante del mouse. In genere, anche l'evento `Clicked` viene generato contemporaneamente all'evento `Released`, ma se il puntatore del dito o del mouse fuoriesce dalla superficie del `ImageButton` prima del rilascio, l'evento `Clicked` potrebbe non verificarsi.

Per ulteriori informazioni su questi eventi, vedere [premere e rilasciare il pulsante](button.md#pressing-and-releasing-the-button) nella guida del [pulsante](button.md) .

## <a name="imagebutton-appearance"></a>Aspetto ImageButton

Oltre alle proprietà che `ImageButton` eredita dalla classe [`View`](xref:Xamarin.Forms.View) , `ImageButton` definisce anche diverse proprietà che influiscono sull'aspetto:

- `Aspect` è il modo in cui l'immagine verrà adattata all'area di visualizzazione.
- `BorderColor` è il colore di un'area che circonda l'`ImageButton`.
- `BorderWidth` è la larghezza del bordo.
- `CornerRadius` è il raggio dell'angolo del `ImageButton`.

È possibile impostare la proprietà `Aspect` su uno dei membri dell'enumerazione [`Aspect`](xref:Xamarin.Forms.Aspect) :

- [`Fill`](xref:Xamarin.Forms.Aspect.Fill) : estende l'immagine in modo che riempia completamente l'`ImageButton`. Questo può comportare l'immagine venga distorta.
- [`AspectFill`](xref:Xamarin.Forms.Aspect.AspectFill) l'immagine viene ritagliata in modo da riempire il `ImageButton` mantenendo le proporzioni.
- [`AspectFit`](xref:Xamarin.Forms.Aspect.AspectFit) -letterbox l'immagine, se necessario, in modo che l'intera immagine venga inserita nel `ImageButton`, con uno spazio vuoto aggiunto alla parte superiore/inferiore o ai lati a seconda che l'immagine sia larga o alta. Si tratta del valore predefinito dell'enumerazione [`Aspect`](xref:Xamarin.Forms.Aspect) .

> [!NOTE]
> La classe `ImageButton` dispone inoltre di proprietà [`Margin`](xref:Xamarin.Forms.View.Margin) e `Padding` che controllano il comportamento del layout del `ImageButton`. Per altre informazioni, vedere l'articolo sulle proprietà [Margin e Padding](~/xamarin-forms/user-interface/layouts/margin-and-padding.md).

## <a name="imagebutton-visual-states"></a>Stati visivi ImageButton

`ImageButton` dispone di un [`VisualState`](xref:Xamarin.Forms.VisualState) `Pressed` che può essere utilizzato per avviare una modifica visiva del `ImageButton` quando viene premuto dall'utente, a condizione che sia abilitato.

Nell'esempio di codice XAML riportato di seguito viene illustrato come definire uno stato di visualizzazione per lo stato `Pressed`:

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

Il `Pressed` [`VisualState`](xref:Xamarin.Forms.VisualState) specifica che, quando viene premuto il `ImageButton`, la relativa proprietà [`Scale`](xref:Xamarin.Forms.VisualElement.Scale) verrà modificata dal valore predefinito da 1 a 0,8. Il `Normal` `VisualState` specifica che quando il `ImageButton` è in uno stato normale, la relativa proprietà `Scale` verrà impostata su 1. Pertanto, l'effetto complessivo è che, quando viene premuto il `ImageButton`, viene ridimensionato in modo da essere leggermente più piccolo e, quando il `ImageButton` viene rilasciato, viene ridimensionato in base alle dimensioni predefinite.

Per ulteriori informazioni sugli stati visivi, vedere [Novell. Forms Visual State Manager](~/xamarin-forms/user-interface/visual-state-manager.md).

## <a name="related-links"></a>Collegamenti correlati

- [Esempio FormsGallery](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/formsgallery)
