---
title: Xamarin. Forms ImageButton
description: Elemento ImageButton Visualizza un'immagine e risponde a un tocco o clic che indirizza un'applicazione per eseguire una determinata attività.
ms.prod: xamarin
ms.assetid: B5906AB6-3F79-4FCB-8C78-1F0AF18AB39E
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/19/2018
ms.openlocfilehash: f97cd3030b865b53b82845ff8941e3f0a10f0320
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61169829"
---
# <a name="xamarinforms-imagebutton"></a>Xamarin. Forms ImageButton

[![Scaricare l'esempio](~/media/shared/download.png) scaricare l'esempio](https://developer.xamarin.com/samples/xamarin-forms/FormsGallery/)

_Elemento ImageButton Visualizza un'immagine e risponde a un tocco o clic che indirizza un'applicazione per eseguire una determinata attività._

Il `ImageButton` visualizzare combina il [ `Button` ](xref:Xamarin.Forms.Button) visualizzazione e [ `Image` ](xref:Xamarin.Forms.Image) vista per creare un pulsante di cui contenuto è un'immagine. L'utente preme il `ImageButton` con un dito o fa clic con il mouse per indirizzare l'applicazione per eseguire un'attività particolare. Tuttavia, a differenza di `Button` visualizzazione, il `ImageButton` vista non dispone di alcun concetto di testo e l'aspetto del testo.

> [!NOTE]
> Mentre il [ `Button` ](xref:Xamarin.Forms.Button) visualizzazione definisce un' [ `Image` ](xref:Xamarin.Forms.Button.Image) proprietà, che consente di visualizzare l'immagine sul `Button`, questa proprietà deve essere utilizzata quando si visualizza un'icona piccola accanto al `Button` testo.

Gli esempi di codice in questa guida vengono prelevati i [FormsGallery esempio](https://developer.xamarin.com/samples/xamarin-forms/FormsGallery/).

## <a name="setting-the-image-source"></a>Impostare l'origine dell'immagine

`ImageButton` definisce un `Source` proprietà che deve essere impostata per l'immagine da visualizzare nel pulsante a, con l'origine dell'immagine che può essere un file, un URI, una risorsa o un flusso. Per altre informazioni sul caricamento di immagini da origini diverse, vedere [immagini in xamarin. Forms](images.md).

Nell'esempio seguente viene mostrato come creare un `ImageButton` in XAML:

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

Il `Source` proprietà consente di specificare l'immagine visualizzata nel `ImageButton`. In questo esempio il valore è impostato in un file locale che verrà caricato da ogni progetto della piattaforma, generando gli screenshot seguenti:

[![Base ImageButton](imagebutton-images/BasicImageButton.png "base ImageButton")](imagebutton-images/BasicImageButton-Large.png#lightbox "ImageButton base")

Per impostazione predefinita, il `ImageButton` rettangolare, ma è possibile assegnare gli angoli arrotondati it usando le `CornerRadius` proprietà. Per altre informazioni sulle `ImageButton` aspetto, vedere [ImageButton aspetto](#imagebutton-appearance).

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

`ImageButton` definisce un `Clicked` evento generato quando l'utente tocca il `ImageButton` con un puntatore con un dito o passare il mouse. L'evento viene generato quando il pulsante con un dito o passare il mouse viene rilasciato dalla superficie del `ImageButton`. Il `ImageButton` deve avere relativi `IsEnabled` impostata su `true` rispondere alle scelte.

Nell'esempio seguente viene illustrato come creare un'istanza di un `ImageButton` in XAML e handle relativo `Clicked` evento:

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

Il `Clicked` è impostato per un gestore eventi denominato `OnImageButtonClicked` che si trova nel file code-behind:

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

Quando la `ImageButton` toccando, il `OnImageButtonClicked` esecuzione del metodo. Il `sender` argomento è il `ImageButton` responsabile di questo evento. Ciò consente di accedere il `ImageButton` oggetto, o per distinguere tra più `ImageButton` gli oggetti che condividono lo stesso `Clicked` evento.

Questa particolare `Clicked` gestore incrementa un contatore e visualizza il valore del contatore in una [ `Label` ](xref:Xamarin.Forms.Label):

[![Fare clic su di base ImageButton](imagebutton-images/ImageButton.png "fare clic su di base ImageButton")](imagebutton-images/ImageButton-Large.png#lightbox "base ImageButton clic")

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

In alcuni casi un'applicazione è in un determinato stato in cui un determinato `ImageButton` clic non è un'operazione valida. In questi casi, il `ImageButton` deve essere disabilitata impostando relativi `IsEnabled` proprietà `false`.

## <a name="using-the-command-interface"></a>Tramite l'interfaccia di comando

È possibile che un'applicazione a cui rispondere `ImageButton` scelte senza gestione il `Clicked` evento. Il `ImageButton` implementa un meccanismo di notifica alternativo denominato il _comando_ oppure _l'esecuzione di comandi_ interfaccia. Questo è costituito da due proprietà:

- `Command` typu [ `ICommand` ](xref:System.Windows.Input.ICommand), un'interfaccia definita nel [ `System.Windows.Input` ](xref:System.Windows.Input) dello spazio dei nomi.
- `CommandParameter` vlastnosti typu [ `Object` ](xref:System.Object).

Questo approccio è adatto in relazione al data binding e in particolare quando si implementa l'architettura Model-View-ViewModel (MVVM).

Per altre informazioni sull'utilizzo dell'interfaccia di comando, vedere [usando l'interfaccia](button.md#using-the-command-interface) nel [pulsante](button.md) Guida.

## <a name="pressing-and-releasing-the-imagebutton"></a>Premere e rilasciare il ImageButton

Altre origini oltre al `Clicked` evento `ImageButton` definisce inoltre `Pressed` e `Released` eventi. Il `Pressed` evento si verifica quando si preme un dito in un `ImageButton`, o si preme un pulsante del mouse con il puntatore posizionato in corrispondenza di `ImageButton`. Il `Released` evento si verifica quando viene rilasciato il pulsante con un dito o passare il mouse. In generale, il `Clicked` evento viene generato allo stesso tempo come i `Released` eventi, ma se il puntatore del mouse o del dito diapositive dalla superficie della `ImageButton` prima di essere rilasciate, il `Clicked` eventi potrebbero non venire eseguiti.

Per altre informazioni su questi eventi, vedere [premere e rilasciare il pulsante](button.md#pressing-and-releasing-the-button) nel [pulsante](button.md) Guida.

## <a name="imagebutton-appearance"></a>Aspetto ImageButton

Oltre alle proprietà che `ImageButton` eredita le [ `View` ](xref:Xamarin.Forms.View) (classe), `ImageButton` definisce inoltre diverse proprietà che interessano l'aspetto del controllo:

- `Aspect` è come l'immagine verrà adattata in base all'area di visualizzazione.
- `BorderColor` rappresenta il colore di un'area che circonda il `ImageButton`.
- `BorderWidth` è la larghezza del bordo.
- `CornerRadius` è il raggio dell'angolo del `ImageButton`.

Il `Aspect` proprietà può essere impostata su uno dei membri del [ `Aspect` ](xref:Xamarin.Forms.Aspect) enumerazione:

- [`Fill`](xref:Xamarin.Forms.Aspect.Fill) -estende l'immagine per riempire completamente ed esattamente il `ImageButton`. Questo può comportare l'immagine venga distorta.
- [`AspectFill`](xref:Xamarin.Forms.Aspect.AspectFill) -Consente di ritagliare l'immagine in modo da riempire il `ImageButton` mantenendo le proporzioni.
- [`AspectFit`](xref:Xamarin.Forms.Aspect.AspectFit) -consegna l'immagine, se necessario, in modo che l'intera immagine si integra il `ImageButton`, con uno spazio vuoto aggiunto per i primi/ultimi o i lati a seconda che l'immagine sia di larghezza e un'altezza. Questo è il valore predefinito di [ `Aspect` ](xref:Xamarin.Forms.Aspect) enumerazione.

> [!NOTE]
> Il `ImageButton` classe dispone inoltre [ `Margin` ](xref:Xamarin.Forms.View.Margin) e `Padding` le proprietà che controllano il comportamento di layout del `ImageButton`. Per altre informazioni, vedere [margine e spaziatura interna](~/xamarin-forms/user-interface/layouts/margin-and-padding.md).

## <a name="imagebutton-visual-states"></a>Stati visivi ImageButton

`ImageButton` è un `Pressed` [ `VisualState` ](xref:Xamarin.Forms.VisualState) che può essere utilizzato per avviare una modifica visiva al `ImageButton` quando premuto dall'utente, condizione che è abilitato.

Nell'esempio XAML seguente viene illustrato come definire uno stato di visualizzazione per il `Pressed` stato:

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

Il `Pressed` [ `VisualState` ](xref:Xamarin.Forms.VisualState) specifica che quando il `ImageButton` viene premuto, relativo [ `Scale` ](xref:Xamarin.Forms.VisualElement.Scale) proprietà verrà modificata il valore predefinito di 1 a 0,8. Il `Normal` `VisualState` specifica che quando il `ImageButton` è in uno stato normale, relativo `Scale` verrà impostata su 1. Pertanto, il risultato complessivo è che quando la `ImageButton` è premuto, viene ridimensionata per essere leggermente più piccoli e quando il `ImageButton` viene rilasciato, viene ridimensionata alle dimensioni predefinite.

Per altre informazioni sugli stati visual, vedere [di xamarin. Forms Visual State Manager](~/xamarin-forms/user-interface/visual-state-manager.md).

## <a name="related-links"></a>Collegamenti correlati

- [Esempio FormsGallery](https://developer.xamarin.com/samples/xamarin-forms/FormsGallery/)
