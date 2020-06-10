---
title: "stili espliciti in Xamarin.Forms " Description: "uno stile esplicito viene applicato selettivamente ai controlli impostando le relative proprietà di stile. In questo articolo viene illustrato come utilizzare stili espliciti in un' Xamarin.Forms applicazione ".
ms. prod: Novell MS. AssetID: C0DF9F8F-B431-4374-A574-325BC3C41A3B ms. Technology: Novell-Forms Author: davidbritch ms. Author: dabritch ms. Date: 02/17/2016 no-loc: [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="explicit-styles-in-xamarinforms"></a>Stili espliciti inXamarin.Forms

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-styles-basicstyles)

_Uno stile esplicito è un oggetto che viene applicato selettivamente ai controlli impostando le relative proprietà di stile._

## <a name="create-an-explicit-style-in-xaml"></a>Creare uno stile esplicito in XAML

Per dichiarare un oggetto [`Style`](xref:Xamarin.Forms.Style) a livello di pagina, [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) è necessario aggiungere un oggetto alla pagina, quindi una o più `Style` dichiarazioni possono essere incluse in `ResourceDictionary` . Un oggetto `Style` viene reso *esplicito* assegnando alla relativa dichiarazione un `x:Key` attributo, che fornisce una chiave descrittiva in `ResourceDictionary` . Gli stili *espliciti* devono quindi essere applicati a elementi visivi specifici impostando le relative [`Style`](xref:Xamarin.Forms.NavigableElement.Style) Proprietà.

Nell'esempio di codice seguente vengono illustrati gli stili *espliciti* dichiarati in XAML in una pagina `ResourceDictionary` e applicati alle istanze della pagina [`Label`](xref:Xamarin.Forms.Label) :

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms" xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml" x:Class="Styles.ExplicitStylesPage" Title="Explicit" IconImageSource="xaml.png">
    <ContentPage.Resources>
        <ResourceDictionary>
            <Style x:Key="labelRedStyle" TargetType="Label">
                <Setter Property="HorizontalOptions"
                        Value="Center" />
                <Setter Property="VerticalOptions"
                        Value="CenterAndExpand" />
                <Setter Property="FontSize" Value="Large" />
                <Setter Property="TextColor" Value="Red" />
            </Style>
            <Style x:Key="labelGreenStyle" TargetType="Label">
                ...
                <Setter Property="TextColor" Value="Green" />
            </Style>
            <Style x:Key="labelBlueStyle" TargetType="Label">
                ...
                <Setter Property="TextColor" Value="Blue" />
            </Style>
        </ResourceDictionary>
    </ContentPage.Resources>
    <ContentPage.Content>
        <StackLayout Padding="0,20,0,0">
            <Label Text="These labels"
                   Style="{StaticResource labelRedStyle}" />
            <Label Text="are demonstrating"
                   Style="{StaticResource labelGreenStyle}" />
            <Label Text="explicit styles,"
                   Style="{StaticResource labelBlueStyle}" />
            <Label Text="and an explicit style override"
                   Style="{StaticResource labelBlueStyle}"
                   TextColor="Teal" />
        </StackLayout>
    </ContentPage.Content>
</ContentPage>
```

[`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary)Definisce tre stili *espliciti* che vengono applicati alle istanze della pagina [`Label`](xref:Xamarin.Forms.Label) . Ogni `Style` viene usato per visualizzare il testo in un colore diverso, impostando anche le dimensioni del carattere e le opzioni di layout orizzontale e verticale. Ogni `Style` viene applicato a un oggetto diverso impostando le `Label` relative [`Style`](xref:Xamarin.Forms.NavigableElement.Style) proprietà usando l' `StaticResource` estensione di markup. Il risultato è l'aspetto illustrato negli screenshot seguenti:

[![Esempio di stili espliciti](explicit-images/explicit-styles.png)](explicit-images/explicit-styles-large.png#lightbox)

Inoltre, all'oggetto finale [`Label`](xref:Xamarin.Forms.Label) è applicato un oggetto [`Style`](xref:Xamarin.Forms.Style) , ma anche la proprietà viene sostituita [`TextColor`](xref:Xamarin.Forms.Label.TextColor) con un `Color` valore diverso.

### <a name="create-an-explicit-style-at-the-control-level"></a>Crea uno stile esplicito a livello di controllo

Oltre a creare stili *espliciti* a livello di pagina, possono anche essere creati a livello di controllo, come illustrato nell'esempio di codice seguente:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms" xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml" x:Class="Styles.ExplicitStylesPage" Title="Explicit" IconImageSource="xaml.png">
    <ContentPage.Content>
        <StackLayout Padding="0,20,0,0">
            <StackLayout.Resources>
                <ResourceDictionary>
                    <Style x:Key="labelRedStyle" TargetType="Label">
                      ...
                    </Style>
                    ...
                </ResourceDictionary>
            </StackLayout.Resources>
            <Label Text="These labels" Style="{StaticResource labelRedStyle}" />
            ...
        </StackLayout>
    </ContentPage.Content>
</ContentPage>
```

In questo esempio, le istanze *esplicite* [`Style`](xref:Xamarin.Forms.Style) vengono assegnate alla [`Resources`](xref:Xamarin.Forms.VisualElement.Resources) raccolta del [`StackLayout`](xref:Xamarin.Forms.StackLayout) controllo. Gli stili possono quindi essere applicati al controllo e ai relativi elementi figlio.

Per informazioni sulla creazione di stili in un'applicazione [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) , vedere [stili globali](~/xamarin-forms/user-interface/styles/application.md).

## <a name="create-an-explicit-style-in-c35"></a>Creare uno stile esplicito in C&#35;

[`Style`](xref:Xamarin.Forms.Style)le istanze possono essere aggiunte alla raccolta di una pagina [`Resources`](xref:Xamarin.Forms.VisualElement.Resources) in C# creando un nuovo oggetto [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) e quindi aggiungendo le `Style` istanze a `ResourceDictionary` , come illustrato nell'esempio di codice seguente:

```csharp
public class ExplicitStylesPageCS : ContentPage
{
    public ExplicitStylesPageCS ()
    {
        var labelRedStyle = new Style (typeof(Label)) {
            Setters = {
                ...
                new Setter { Property = Label.TextColorProperty, Value = Color.Red    }
            }
        };
        var labelGreenStyle = new Style (typeof(Label)) {
            Setters = {
                ...
                new Setter { Property = Label.TextColorProperty, Value = Color.Green }
            }
        };
        var labelBlueStyle = new Style (typeof(Label)) {
            Setters = {
                ...
                new Setter { Property = Label.TextColorProperty, Value = Color.Blue }
            }
        };

        Resources = new ResourceDictionary ();
        Resources.Add ("labelRedStyle", labelRedStyle);
        Resources.Add ("labelGreenStyle", labelGreenStyle);
        Resources.Add ("labelBlueStyle", labelBlueStyle);
        ...

        Content = new StackLayout {
            Children = {
                new Label { Text = "These labels",
                            Style = (Style)Resources ["labelRedStyle"] },
                new Label { Text = "are demonstrating",
                            Style = (Style)Resources ["labelGreenStyle"] },
                new Label { Text = "explicit styles,",
                            Style = (Style)Resources ["labelBlueStyle"] },
                new Label {    Text = "and an explicit style override",
                            Style = (Style)Resources ["labelBlueStyle"], TextColor = Color.Teal }
            }
        };
    }
}
```

Il costruttore definisce tre stili *espliciti* che vengono applicati alle istanze della pagina [`Label`](xref:Xamarin.Forms.Label) . Ogni *esplicito* [`Style`](xref:Xamarin.Forms.Style) viene aggiunto all'oggetto [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) usando il [`Add`](xref:Xamarin.Forms.ResourceDictionary.Add(System.String,System.Object)) metodo, specificando una `key` stringa per fare riferimento all' `Style` istanza. Ogni `Style` viene applicato a un oggetto diverso impostando `Label` le relative [`Style`](xref:Xamarin.Forms.NavigableElement.Style) Proprietà.

Tuttavia, non vi è alcun vantaggio nell'uso di [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) qui. Al contrario, [`Style`](xref:Xamarin.Forms.Style) le istanze possono essere assegnate direttamente alle [`Style`](xref:Xamarin.Forms.NavigableElement.Style) proprietà degli elementi visivi richiesti e `ResourceDictionary` possono essere rimosse, come illustrato nell'esempio di codice seguente:

```csharp
public class ExplicitStylesPageCS : ContentPage
{
    public ExplicitStylesPageCS ()
    {
        var labelRedStyle = new Style (typeof(Label)) {
            ...
        };
        var labelGreenStyle = new Style (typeof(Label)) {
            ...
        };
        var labelBlueStyle = new Style (typeof(Label)) {
            ...
        };
        ...
        Content = new StackLayout {
            Children = {
                new Label { Text = "These labels", Style = labelRedStyle },
                new Label { Text = "are demonstrating", Style = labelGreenStyle },
                new Label { Text = "explicit styles,", Style = labelBlueStyle },
                new Label { Text = "and an explicit style override", Style = labelBlueStyle,
                            TextColor = Color.Teal }
            }
        };
    }
}
```

Il costruttore definisce tre stili *espliciti* che vengono applicati alle istanze della pagina [`Label`](xref:Xamarin.Forms.Label) . Ogni `Style` viene usato per visualizzare il testo in un colore diverso, impostando anche le dimensioni del carattere e le opzioni di layout orizzontale e verticale. Ogni `Style` viene applicato a un oggetto diverso impostando `Label` le relative [`Style`](xref:Xamarin.Forms.NavigableElement.Style) Proprietà. Inoltre, all'oggetto finale `Label` è applicato un oggetto `Style` , ma anche la proprietà viene sostituita `TextColor` con un `Color` valore diverso.

## <a name="related-links"></a>Collegamenti correlati

- [Estensioni di markup XAML](~/xamarin-forms/xaml/xaml-basics/xaml-markup-extensions.md)
- [Stili di base (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-styles-basicstyles)
- [Utilizzo degli stili (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithstyles)
- [ResourceDictionary](xref:Xamarin.Forms.ResourceDictionary)
- [Style](xref:Xamarin.Forms.Style)
- [Setter](xref:Xamarin.Forms.Setter)
