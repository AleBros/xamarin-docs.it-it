---
title: Xamarin.FormsExpander
description: Il Xamarin.Forms controllo Expander fornisce un contenitore espandibile per ospitare qualsiasi contenuto. Per visualizzare o nascondere il contenuto, toccare l'intestazione di espansione.
ms.prod: xamarin
ms.assetid: 381DCB55-522D-4414-B45B-E8DD70AA9985
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/15/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 33659dd52452c575c403d0a25b24f17daf9e3f17
ms.sourcegitcommit: 8a18471b3d96f3f726b66f9bc50a829f1c122f29
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/18/2020
ms.locfileid: "84988222"
---
# <a name="xamarinforms-expander"></a>Xamarin.FormsExpander

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-expanderdemos/)

Il Xamarin.Forms `Expander` controllo fornisce un contenitore espandibile per ospitare qualsiasi contenuto. Il controllo ha un'intestazione e un contenuto e il contenuto viene visualizzato o nascosto toccando l' `Expander` intestazione. Quando `Expander` viene visualizzata solo l'intestazione, `Expander` viene *compresso*. Quando il `Expander` contenuto è visibile, l'oggetto `Expander` è *espanso*.

Gli screenshot seguenti mostrano un `Expander` negli Stati compresso ed espanso, con le caselle rosse che indicano l'intestazione e il contenuto:

![Screenshot di un espansore negli Stati compresso ed espanso, in iOS e Android](expander-images/expander.png "Espansore in iOS e Android")

> [!IMPORTANT]
> `Expander`è attualmente sperimentale e può essere usato solo impostando il `Expander_Experimental` flag. Per ulteriori informazioni, vedere [flag sperimentali](~/xamarin-forms/internals/experimental-flags.md).
>
> Inoltre, il `Expander` controllo è completamente implementato nello `Xamarin.Forms` spazio dei nomi. Pertanto, è disponibile su tutte le piattaforme supportate da Xamarin.Forms .

Il `Expander` controllo definisce le proprietà seguenti:

- `CollapseAnimationEasing`, di tipo [`Easing`](xref:Xamarin.Forms.Easing) , che rappresenta la funzione di interpolazione da applicare al `Expander` contenuto quando è in fase di compressione.
- `CollapseAnimationLength`, di tipo `uint` , che definisce la durata dell'animazione quando è in fase di `Expander` compressione. Il valore predefinito di questa proprietà è 250ms.
- `Command`, di tipo `ICommand` , che viene eseguito quando l' `Expander` intestazione viene toccata.
- `CommandParameter`, di tipo `object`, ovvero il parametro passato a `Command`.
- `Content`, di tipo [`View`](xref:Xamarin.Forms.View) , che definisce il contenuto da visualizzare quando viene `Expander` espanso.
- `ContentTemplate`, di tipo [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) , che è il modello usato per ingrandire dinamicamente il contenuto di `Expander` .
- `ExpandAnimationEasing`, di tipo [`Easing`](xref:Xamarin.Forms.Easing) , che rappresenta la funzione di interpolazione da applicare al `Expander` contenuto durante l'espansione.
- `ExpandAnimationLength`, di tipo `uint` , che definisce la durata dell'animazione quando si `Expander` espande. Il valore predefinito di questa proprietà è 250ms.
- `ForceUpdateSizeCommand`, di tipo `ICommand` , che definisce il comando che viene eseguito quando viene forzata l'aggiornamento della dimensione di `Expander` . Questa proprietà utilizza la `OneWayToSource` modalità di associazione.
- `Header`, di tipo [`View`](xref:Xamarin.Forms.View) , che definisce il contenuto dell'intestazione.
- `IsExpanded`, di tipo `bool` , che determina se l'oggetto `Expander` è espanso. Questa proprietà utilizza la `TwoWay` modalità di associazione e il valore predefinito è `false` .
- `State`, di tipo `ExpanderState` , che rappresenta lo stato dell'oggetto `Expander` . Questa proprietà utilizza la `OneWayToSource` modalità di associazione.

Queste proprietà sono supportate da [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) oggetti, il che significa che possono essere destinazioni di data binding e con stile.

> [!NOTE]
> La `Content` proprietà è la proprietà Content della `Expander` classe e pertanto non deve essere impostata in modo esplicito da XAML.

L'enumerazione `ExpanderState` definisce i membri seguenti:

- `Expanding`indica che l'oggetto `Expander` è in espansione.
- `Expanded`indica che l'oggetto `Expander` è espanso.
- `Collapsing`indica che l'oggetto `Expander` sta collassando.
- `Collapsed`indica che l'oggetto `Expander` è compresso.

Il `Expander` controllo definisce anche un `Tapped` evento che viene generato quando `Expander` viene toccata l'intestazione. `Expander`Include inoltre un `ForceUpdateSize` metodo che può essere chiamato per ridimensionare a livello di codice `Expander` in fase di esecuzione.

## <a name="create-an-expander"></a>Creare un espansore

Nell'esempio seguente viene illustrato come creare un'istanza di `Expander` in XAML:

```xaml
<Expander>
    <Expander.Header>
        <Label Text="Baboon"
               FontAttributes="Bold"
               FontSize="Medium" />
    </Expander.Header>
    <Grid Padding="10">
        <Grid.ColumnDefinitions>
            <ColumnDefinition Width="Auto" />
            <ColumnDefinition Width="Auto" />
        </Grid.ColumnDefinitions>
        <Image Source="http://upload.wikimedia.org/wikipedia/commons/thumb/f/fc/Papio_anubis_%28Serengeti%2C_2009%29.jpg/200px-Papio_anubis_%28Serengeti%2C_2009%29.jpg"
               Aspect="AspectFill"
               HeightRequest="120"
               WidthRequest="120" />
        <Label Grid.Column="1"
               Text="Baboons are African and Arabian Old World monkeys belonging to the genus Papio, part of the subfamily Cercopithecinae."
               FontAttributes="Italic" />
    </Grid>
</Expander>
```

In questo esempio, `Expander` è compresso per impostazione predefinita e visualizza [`Label`](xref:Xamarin.Forms.Label) come intestazione. Quando si tocca l'intestazione `Expander` , si ottiene l'espansione per rivelare il contenuto, ovvero un [`Grid`](xref:Xamarin.Forms.Grid) controllo figlio contenitore. Quando `Expander` è espanso, toccando la relativa intestazione viene compresso `Expander` .

> [!IMPORTANT]
> Quando si imposta la `Expander.Content` proprietà, in modo implicito o esplicito, il `Expander` contenuto viene creato quando si passa alla pagina che lo contiene, anche se `Expander` è compresso. Tuttavia, la `Expander.ContentTemplate` proprietà può essere impostata su contenuto che viene inflat solo quando si `Expander` espande per la prima volta. Per altre informazioni, vedere [creare un contenuto Expander su richiesta](#create-expander-content-on-demand).

In alternativa, `Expander` è possibile creare un oggetto nel codice:

```csharp
Expander expander = new Expander
{
    Header = new Label
    {
        Text = "Baboon",
        FontAttributes = FontAttributes.Bold,
        FontSize = Device.GetNamedSize(NamedSize.Medium, typeof(Label))
    }
};

Grid grid = new Grid
{
    Padding = new Thickness(10),
    ColumnDefinitions =
    {
        new ColumnDefinition { Width = GridLength.Auto },
        new ColumnDefinition { Width = GridLength.Auto }
    }
};

grid.Children.Add(new Image
{
    Source = "http://upload.wikimedia.org/wikipedia/commons/thumb/f/fc/Papio_anubis_%28Serengeti%2C_2009%29.jpg/200px-Papio_anubis_%28Serengeti%2C_2009%29.jpg",
    Aspect = Aspect.AspectFill,
    HeightRequest = 120,
    WidthRequest = 120
});

grid.Children.Add(new Label
{
    Text = "Baboons are African and Arabian Old World monkeys belonging to the genus Papio, part of the subfamily Cercopithecinae.",
    FontAttributes = FontAttributes.Italic
}, 1, 0);

expander.Content = grid;
```

## <a name="create-expander-content-on-demand"></a>Crea contenuto Expander su richiesta

`Expander`il contenuto può essere creato su richiesta, in risposta alla `Expander` espansione. Questa operazione può essere eseguita impostando la `Expander.ContentTemplate` proprietà su un oggetto [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) che contiene il contenuto:

```xaml
<Expander>
    <Expander.Header>
        <Label Text="{Binding Name}"
               FontAttributes="Bold"
               FontSize="Medium" />
    </Expander.Header>
    <Expander.ContentTemplate>
        <DataTemplate>
            <Grid Padding="10">
                <Grid.ColumnDefinitions>
                    <ColumnDefinition Width="Auto" />
                    <ColumnDefinition Width="Auto" />
                </Grid.ColumnDefinitions>
                <Image Source="{Binding ImageUrl}"
                       Aspect="AspectFill"
                       HeightRequest="120"
                       WidthRequest="120" />
                <Label Grid.Column="1"
                       Text="{Binding Details}"
                       FontAttributes="Italic" />
            </Grid>
        </DataTemplate>
    </Expander.ContentTemplate>
</Expander>
```

In questo esempio, il `Expander` contenuto viene inflat solo quando si `Expander` espande per la prima volta.

Il vantaggio di questo approccio consiste nel fatto che quando una pagina contiene più `Expander` oggetti, il contenuto per un `Expander` viene creato solo quando viene espanso per la prima volta dall'utente.

## <a name="add-an-expansion-indicator"></a>Aggiungere un indicatore di espansione

Un oggetto [`Image`](xref:Xamarin.Forms.Image) può essere aggiunto a un' `Expander` intestazione per fornire un'indicazione visiva dello stato di espansione. Un oggetto [`DataTrigger`](xref:Xamarin.Forms.DataTrigger) può essere collegato a `Image` , che modifica la `Source` Proprietà in base al valore della `Expander.IsExpanded` proprietà:

```xaml
<Expander>
    <Expander.Header>
        <Grid>
            <Label Text="{Binding Name}"
                   FontAttributes="Bold"
                   FontSize="Medium" />
            <Image Source="expand.png"
                   HorizontalOptions="End"
                   VerticalOptions="Start">
                <Image.Triggers>
                    <DataTrigger TargetType="Image"
                                 Binding="{Binding Source={RelativeSource AncestorType={x:Type Expander}}, Path=IsExpanded}"
                                 Value="True">
                        <Setter Property="Source"
                                Value="collapse.png" />
                    </DataTrigger>
                </Image.Triggers>
            </Image>
        </Grid>
    </Expander.Header>
    <Expander.ContentTemplate>
        <DataTemplate>
            <Grid Padding="10">
                <Grid.ColumnDefinitions>
                    <ColumnDefinition Width="Auto" />
                    <ColumnDefinition Width="Auto" />
                </Grid.ColumnDefinitions>
                <Image Source="{Binding ImageUrl}"
                       Aspect="AspectFill"
                       HeightRequest="120"
                       WidthRequest="120" />
                <Label Grid.Column="1"
                       Text="{Binding Details}"
                       FontAttributes="Italic" />
            </Grid>
        </DataTemplate>
    </Expander.ContentTemplate>
</Expander>
```

In questo esempio, [`Image`](xref:Xamarin.Forms.Image) Visualizza l' `expand` icona per impostazione predefinita:

![Screenshot di un'icona di espansione in stato compresso, in iOS e Android](expander-images/icon-expand.png "Icona espansa in iOS e Android")

La `IsExpanded` proprietà diventa `true` quando `Expander` viene toccata l'intestazione, il che comporta la `collapse` visualizzazione dell'icona:

![Screenshot di un'icona di espansione nello stato Expand, in iOS e Android](expander-images/icon-collapse.png "Icona espansa in iOS e Android")

Per ulteriori informazioni sui trigger, vedere [ Xamarin.Forms trigger](~/xamarin-forms/app-fundamentals/triggers.md).

## <a name="embed-an-expander-in-an-expander"></a>Incorporare un espansore in un espansore

Il contenuto di un oggetto `Expander` può essere impostato su un altro `Expander` controllo per abilitare più livelli di espansione. Il codice XAML seguente mostra un `Expander` oggetto il cui contenuto è un altro `Expander` oggetto:

```xaml
<Expander>
    <Expander.Header>
        <Label Text="{Binding Name}"
               FontAttributes="Bold"
               FontSize="Medium" />
    </Expander.Header>
    <Expander Padding="10">
        <Expander.Header>
            <Label Text="{Binding Location}"
                   FontSize="Medium" />
        </Expander.Header>
            <Expander.ContentTemplate>
                <DataTemplate>
                    <Grid>
                        <Grid.ColumnDefinitions>
                            <ColumnDefinition Width="Auto" />
                            <ColumnDefinition Width="Auto" />
                        </Grid.ColumnDefinitions>
                        <Image Source="{Binding ImageUrl}"
                               Aspect="AspectFill"
                               HeightRequest="120"
                               WidthRequest="120" />
                        <Label Grid.Column="1"
                               Text="{Binding Details}"
                               FontAttributes="Italic" />
                    </Grid>
                </DataTemplate>
            </Expander.ContentTemplate>
    </Expander>
</Expander>
```

In questo esempio, toccando l'intestazione radice viene visualizzata `Expander` l'intestazione per l'elemento figlio `Expander` :

![Screenshot di un espansore incorporato, in iOS e Android](expander-images/embedded-expander1.png "Espansore incorporato in iOS e Android")

Toccando l'intestazione figlio si `Expander` ottiene un contenuto inflat e visualizzato:

![Screenshot di un espansore incorporato, in iOS e Android](expander-images/embedded-expander2.png "Espansore incorporato in iOS e Android")

## <a name="define-the-expand-and-collapse-animation"></a>Definire l'animazione Espandi e Comprimi

L'animazione che si verifica quando si `Expander` espande o si comprime può essere definita impostando le `ExpandAnimationEasing` `CollapseAnimationEasing` proprietà e su qualsiasi funzione di interpolazione inclusa in Xamarin.Forms o funzioni di interpolazione personalizzate. Per impostazione predefinita, le animazioni di espansione e compressione si verificano tramite 250ms. Tuttavia, è possibile modificare queste durate impostando le `ExpandAnimationLength` `CollapseAnimationLength` proprietà e su `uint` valori.

Il codice XAML seguente mostra un esempio di definizione dell'animazione che si verifica quando `Expander` viene espanso o compresso dall'utente:

```xaml
<Expander ExpandAnimationEasing="{x:Static Easing.CubicIn}"
          ExpandAnimationLength="500"
          CollapseAnimationEasing="{x:Static Easing.CubicOut}"
          CollapseAnimationLength="500">
    <Expander.Header>
        <Label Text="{Binding Name}"
               FontAttributes="Bold"
               FontSize="Medium" />
    </Expander.Header>
    <Expander.ContentTemplate>
        <DataTemplate>
            <Grid Padding="10">
                <Grid.ColumnDefinitions>
                    <ColumnDefinition Width="Auto" />
                    <ColumnDefinition Width="Auto" />
                </Grid.ColumnDefinitions>
                <Image Source="{Binding ImageUrl}"
                       Aspect="AspectFill"
                       HeightRequest="120"
                       WidthRequest="120" />
                <Label Grid.Column="1"
                       Text="{Binding Details}"
                       FontAttributes="Italic" />
            </Grid>
        </DataTemplate>
    </Expander.ContentTemplate>
</Expander>
```

In questo esempio, la `CubicIn` funzione di interpolazione accelera lentamente l'animazione di espansione su 500 ms e la `CubicOut` funzione di interpolazione rallenta rapidamente l'animazione di compressione su 500 ms.

Per ulteriori informazioni sulle funzioni di interpolazione, vedere [ Xamarin.Forms funzioni di interpolazione](~/xamarin-forms/user-interface/animation/easing.md).

## <a name="resize-an-expander-at-runtime"></a>Ridimensionare un espansore in fase di esecuzione

Un oggetto `Expander` può essere ridimensionato a livello di codice in fase di esecuzione con il `ForceUpdateSize` metodo.

Dato un oggetto `Expander` denominato `expander` , il cui contenuto include un oggetto a [`Label`](xref:Xamarin.Forms.Label) cui è associato un oggetto `TapGestureRecognizer` , nell'esempio di codice seguente viene illustrata la chiamata al `ForceUpdateSize` Metodo:

```csharp
void OnLabelTapped(object sender, EventArgs e)
{
    Label label = sender as Label;
    Expander expander = label.Parent.Parent.Parent as Expander;

    if (label.FontSize == Device.GetNamedSize(NamedSize.Default, label))
    {
        label.FontSize = Device.GetNamedSize(NamedSize.Large, label);
    }
    else
    {
        label.FontSize = Device.GetNamedSize(NamedSize.Default, label);
    }
    expander.ForceUpdateSize();
}
```

In questo esempio, l'oggetto `FontSize` di un oggetto viene [`Label`](xref:Xamarin.Forms.Label) modificato quando `Label` viene toccato. A causa della dimensione della modifica del tipo di carattere, è necessario aggiornare le dimensioni di chiamando il `Expander` relativo `ForceUpdateSize` metodo.

## <a name="disable-an-expander"></a>Disabilitare un espansore

Un'applicazione può entrare in uno stato in cui l'espansione di un `Expander` non è un'operazione valida. In questi casi, `Expander` può essere disabilitato impostando la `IsEnabled` proprietà su false. Ciò impedirà agli utenti di espandere o comprimere il `Expander` .

## <a name="related-links"></a>Collegamenti correlati

- [Demo Expander (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-expanderdemos/)
- [Xamarin.FormsFunzioni di interpolazione](~/xamarin-forms/user-interface/animation/easing.md)
- [Xamarin.FormsTrigger](~/xamarin-forms/app-fundamentals/triggers.md)
- [Xamarin.FormsLayout associabili](~/xamarin-forms/user-interface/layouts/bindable-layouts.md)
