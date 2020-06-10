---
title: "stili impliciti in Xamarin.Forms " Description: "uno stile implicito viene usato da tutti i controlli dello stesso TargetType, senza richiedere a ogni controllo di fare riferimento allo stile".
ms. prod: Novell MS. AssetID: 02A75F3B-4389-49D4-A2F4-AFD473A4A161 ms. Technology: Novell-Forms Author: davidbritch ms. Author: dabritch ms. Date: 01/30/2019 no-loc: [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="implicit-styles-in-xamarinforms"></a>Stili impliciti inXamarin.Forms

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-styles-basicstyles)

_Uno stile implicito è quello usato da tutti i controlli dello stesso TargetType, senza richiedere a ogni controllo di fare riferimento allo stile._

## <a name="create-an-implicit-style-in-xaml"></a>Creare uno stile implicito in XAML

Per dichiarare un oggetto [`Style`](xref:Xamarin.Forms.Style) a livello di pagina, [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) è necessario aggiungere un oggetto alla pagina, quindi una o più `Style` dichiarazioni possono essere incluse in `ResourceDictionary` . Un `Style` viene reso *implicito* non specificando un `x:Key` attributo. Lo stile verrà quindi applicato agli elementi visivi che corrispondono `TargetType` esattamente a elementi che derivano dal `TargetType` valore.

Nell'esempio di codice seguente viene illustrato uno stile *implicito* dichiarato in XAML in una pagina `ResourceDictionary` e applicato alle istanze della pagina [`Entry`](xref:Xamarin.Forms.Entry) :

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms" xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml" xmlns:local="clr-namespace:Styles;assembly=Styles" x:Class="Styles.ImplicitStylesPage" Title="Implicit" IconImageSource="xaml.png">
    <ContentPage.Resources>
        <ResourceDictionary>
            <Style TargetType="Entry">
                <Setter Property="HorizontalOptions" Value="Fill" />
                <Setter Property="VerticalOptions" Value="CenterAndExpand" />
                <Setter Property="BackgroundColor" Value="Yellow" />
                <Setter Property="FontAttributes" Value="Italic" />
                <Setter Property="TextColor" Value="Blue" />
            </Style>
        </ResourceDictionary>
    </ContentPage.Resources>
    <ContentPage.Content>
        <StackLayout Padding="0,20,0,0">
            <Entry Text="These entries" />
            <Entry Text="are demonstrating" />
            <Entry Text="implicit styles," />
            <Entry Text="and an implicit style override" BackgroundColor="Lime" TextColor="Red" />
            <local:CustomEntry Text="Subclassed Entry is not receiving the style" />
        </StackLayout>
    </ContentPage.Content>
</ContentPage>
```

[`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary)Definisce un singolo stile *implicito* che viene applicato alle istanze della pagina [`Entry`](xref:Xamarin.Forms.Entry) . `Style`Viene usato per visualizzare il testo blu su uno sfondo giallo, impostando anche altre opzioni relative all'aspetto. L'oggetto `Style` viene aggiunto all'oggetto della pagina [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) senza specificare un `x:Key` attributo. Pertanto, `Style` viene applicato a tutte le `Entry` istanze in modo implicito in quanto corrispondono alla [`TargetType`](xref:Xamarin.Forms.Style.TargetType) proprietà dell'oggetto `Style` esattamente. Tuttavia, l'oggetto `Style` non viene applicato all' `CustomEntry` istanza di, che è una sottoclasse `Entry` . Il risultato è l'aspetto illustrato negli screenshot seguenti:

[![Esempio di stili impliciti](implicit-images/implicit-styles.png)](implicit-images/implicit-styles-large.png#lightbox)

Il quarto, inoltre, [`Entry`](xref:Xamarin.Forms.Entry) esegue l'override delle [`BackgroundColor`](xref:Xamarin.Forms.VisualElement.BackgroundColor) [`TextColor`](xref:Xamarin.Forms.InputView.TextColor) proprietà e dello stile implicito in `Color` valori diversi.

### <a name="create-an-implicit-style-at-the-control-level"></a>Creare uno stile implicito a livello di controllo

Oltre a creare stili *impliciti* a livello di pagina, possono anche essere creati a livello di controllo, come illustrato nell'esempio di codice seguente:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms" xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml" xmlns:local="clr-namespace:Styles;assembly=Styles" x:Class="Styles.ImplicitStylesPage" Title="Implicit" IconImageSource="xaml.png">
    <ContentPage.Content>
        <StackLayout Padding="0,20,0,0">
            <StackLayout.Resources>
                <ResourceDictionary>
                    <Style TargetType="Entry">
                        <Setter Property="HorizontalOptions" Value="Fill" />
                        ...
                    </Style>
                </ResourceDictionary>
            </StackLayout.Resources>
            <Entry Text="These entries" />
            ...
        </StackLayout>
    </ContentPage.Content>
</ContentPage>
```

In questo esempio, l'oggetto *implicito* [`Style`](xref:Xamarin.Forms.Style) viene assegnato alla [`Resources`](xref:Xamarin.Forms.VisualElement.Resources) raccolta del [`StackLayout`](xref:Xamarin.Forms.StackLayout) controllo. Lo stile *implicito* può quindi essere applicato al controllo e ai relativi elementi figlio.

Per informazioni sulla creazione di stili in un'applicazione [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) , vedere [stili globali](~/xamarin-forms/user-interface/styles/application.md).

## <a name="create-an-implicit-style-in-c35"></a>Creare uno stile implicito in C&#35;

[`Style`](xref:Xamarin.Forms.Style)le istanze possono essere aggiunte alla raccolta di una pagina [`Resources`](xref:Xamarin.Forms.VisualElement.Resources) in C# creando un nuovo oggetto [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) e quindi aggiungendo le `Style` istanze a `ResourceDictionary` , come illustrato nell'esempio di codice seguente:

```csharp
public class ImplicitStylesPageCS : ContentPage
{
    public ImplicitStylesPageCS ()
    {
        var entryStyle = new Style (typeof(Entry)) {
            Setters = {
                ...
                new Setter { Property = Entry.TextColorProperty, Value = Color.Blue }
            }
        };

        ...
        Resources = new ResourceDictionary ();
        Resources.Add (entryStyle);

        Content = new StackLayout {
            Children = {
                new Entry { Text = "These entries" },
                new Entry { Text = "are demonstrating" },
                new Entry { Text = "implicit styles," },
                new Entry { Text = "and an implicit style override", BackgroundColor = Color.Lime, TextColor = Color.Red },
                new CustomEntry  { Text = "Subclassed Entry is not receiving the style" }
            }
        };
    }
}
```

Il costruttore definisce un unico stile *implicito* che viene applicato alle istanze della pagina [`Entry`](xref:Xamarin.Forms.Entry) . `Style`Viene usato per visualizzare il testo blu su uno sfondo giallo, impostando anche altre opzioni relative all'aspetto. L'oggetto `Style` viene aggiunto all'oggetto della pagina [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) senza specificare una `key` stringa. Pertanto, `Style` viene applicato a tutte le `Entry` istanze in modo implicito in quanto corrispondono alla [`TargetType`](xref:Xamarin.Forms.Style.TargetType) proprietà dell'oggetto `Style` esattamente. Tuttavia, l'oggetto `Style` non viene applicato all' `CustomEntry` istanza di, che è una sottoclasse `Entry` .

## <a name="apply-a-style-to-derived-types"></a>Applicare uno stile ai tipi derivati

La [`Style.ApplyToDerivedTypes`](xref:Xamarin.Forms.Style.ApplyToDerivedTypes) proprietà consente di applicare uno stile ai controlli derivati dal tipo di base a cui fa riferimento la [`TargetType`](xref:Xamarin.Forms.Style.TargetType) Proprietà. L'impostazione di questa proprietà su `true` consente pertanto a un singolo stile di avere come destinazione più tipi, purché i tipi derivano dal tipo di base specificato nella `TargetType` Proprietà.

Nell'esempio seguente viene illustrato uno stile implicito che imposta il colore di sfondo delle [`Button`](xref:Xamarin.Forms.Button) istanze su rosso:

```xaml
<Style TargetType="Button"
       ApplyToDerivedTypes="True">
    <Setter Property="BackgroundColor"
            Value="Red" />
</Style>
```

Se si posiziona questo stile in un livello di pagina [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) , questo verrà applicato a tutte le [`Button`](xref:Xamarin.Forms.Button) istanze nella pagina e anche a tutti i controlli che derivano da `Button` . Tuttavia, se la [`ApplyToDerivedTypes`](xref:Xamarin.Forms.Style.ApplyToDerivedTypes) proprietà non è stata impostata, lo stile verrebbe applicato solo alle `Button` istanze.

Il codice C# equivalente è il seguente:

```csharp
var buttonStyle = new Style(typeof(Button))
{
    ApplyToDerivedTypes = true,
    Setters =
    {
        new Setter
        {
            Property = VisualElement.BackgroundColorProperty,
            Value = Color.Red
        }
    }
};

Resources = new ResourceDictionary { buttonStyle };
```

## <a name="related-links"></a>Collegamenti correlati

- [Estensioni di markup XAML](~/xamarin-forms/xaml/xaml-basics/xaml-markup-extensions.md)
- [Stili di base (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-styles-basicstyles)
- [Utilizzo degli stili (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithstyles)
- [ResourceDictionary](xref:Xamarin.Forms.ResourceDictionary)
- [Style](xref:Xamarin.Forms.Style)
- [Setter](xref:Xamarin.Forms.Setter)
