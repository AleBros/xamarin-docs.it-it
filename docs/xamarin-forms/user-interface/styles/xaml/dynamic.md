---
title: "stili dinamici in Xamarin.Forms " Description: "questo articolo spiega in che modo un' Xamarin.Forms applicazione può rispondere in modo dinamico alle modifiche di stile in fase di esecuzione usando risorse dinamiche".
ms. prod: Novell MS. AssetID: 13D4FA4B-DF10-42BF-B001-2C49367FC216 ms. Technology: Novell-Forms Author: davidbritch ms. Author: dabritch ms. Date: 05/28/2019 no-loc: [ Xamarin.Forms , Xamarin.Essentials ] ms. Custom: video
---

# <a name="dynamic-styles-in-xamarinforms"></a>Stili dinamici inXamarin.Forms

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-styles-dynamicstyles)

_Gli stili non rispondono alle modifiche delle proprietà e rimangono invariati per la durata di un'applicazione. Ad esempio, dopo avere assegnato uno stile a un elemento visivo, se una delle istanze del setter viene modificata, rimossa o se è stata aggiunta una nuova istanza di setter, le modifiche non verranno applicate all'elemento visivo. Tuttavia, le applicazioni possono rispondere dinamicamente alle modifiche di stile in fase di esecuzione tramite risorse dinamiche._

L' `DynamicResource` estensione di markup è simile all' `StaticResource` estensione di markup in quanto entrambi usano una chiave del dizionario per recuperare un valore da un oggetto [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) . Tuttavia, mentre `StaticResource` esegue una singola ricerca nel dizionario, `DynamicResource` mantiene un collegamento alla chiave del dizionario. Pertanto, se la voce del dizionario associata alla chiave viene sostituita, la modifica viene applicata all'elemento visivo. Ciò consente di apportare modifiche allo stile di runtime in un'applicazione.

Nell'esempio di codice seguente vengono illustrati gli stili *dinamici* in una pagina XAML:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms" xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml" x:Class="Styles.DynamicStylesPage" Title="Dynamic" IconImageSource="xaml.png">
    <ContentPage.Resources>
        <ResourceDictionary>
            <Style x:Key="baseStyle" TargetType="View">
              ...
            </Style>
            <Style x:Key="blueSearchBarStyle"
                   TargetType="SearchBar"
                   BasedOn="{StaticResource baseStyle}">
              ...
            </Style>
            <Style x:Key="greenSearchBarStyle"
                   TargetType="SearchBar">
              ...
            </Style>
            ...
        </ResourceDictionary>
    </ContentPage.Resources>
    <ContentPage.Content>
        <StackLayout Padding="0,20,0,0">
            <SearchBar Placeholder="These SearchBar controls"
                       Style="{DynamicResource searchBarStyle}" />
            ...
        </StackLayout>
    </ContentPage.Content>
</ContentPage>
```

Le [`SearchBar`](xref:Xamarin.Forms.SearchBar) istanze usano l' `DynamicResource` estensione di markup per fare riferimento a un oggetto [`Style`](xref:Xamarin.Forms.Style) denominato `searchBarStyle` , che non è definito in XAML. Tuttavia, poiché le [`Style`](xref:Xamarin.Forms.NavigableElement.Style) proprietà delle `SearchBar` istanze vengono impostate utilizzando un oggetto `DynamicResource` , la chiave del dizionario mancante non comporta la generazione di un'eccezione.

Al contrario, nel file code-behind, il costruttore crea una [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) voce con la chiave `searchBarStyle` , come illustrato nell'esempio di codice seguente:

```csharp
public partial class DynamicStylesPage : ContentPage
{
    bool originalStyle = true;

    public DynamicStylesPage ()
    {
        InitializeComponent ();
        Resources ["searchBarStyle"] = Resources ["blueSearchBarStyle"];
    }

    void OnButtonClicked (object sender, EventArgs e)
    {
        if (originalStyle) {
            Resources ["searchBarStyle"] = Resources ["greenSearchBarStyle"];
            originalStyle = false;
        } else {
            Resources ["searchBarStyle"] = Resources ["blueSearchBarStyle"];
            originalStyle = true;
        }
    }
}
```

Quando `OnButtonClicked` viene eseguito il gestore eventi, `searchBarStyle` passa tra `blueSearchBarStyle` e `greenSearchBarStyle` . Il risultato è l'aspetto illustrato negli screenshot seguenti:

[ ![ ](dynamic-images/dynamic-style-blue.png)](dynamic-images/dynamic-style-blue-large.png#lightbox)Esempio di 
 [ ![ stile dinamico verde](dynamic-images/dynamic-style-green.png)](dynamic-images/dynamic-style-green-large.png#lightbox) di esempio di stile dinamico blu

Nell'esempio di codice seguente viene illustrata la pagina equivalente in C#:

```csharp
public class DynamicStylesPageCS : ContentPage
{
    bool originalStyle = true;

    public DynamicStylesPageCS ()
    {
        ...
        var baseStyle = new Style (typeof(View)) {
            ...
        };
        var blueSearchBarStyle = new Style (typeof(SearchBar)) {
            ...
        };
        var greenSearchBarStyle = new Style (typeof(SearchBar)) {
            ...
        };
        ...
        var searchBar1 = new SearchBar { Placeholder = "These SearchBar controls" };
        searchBar1.SetDynamicResource (VisualElement.StyleProperty, "searchBarStyle");
        ...
        Resources = new ResourceDictionary ();
        Resources.Add ("blueSearchBarStyle", blueSearchBarStyle);
        Resources.Add ("greenSearchBarStyle", greenSearchBarStyle);
        Resources ["searchBarStyle"] = Resources ["blueSearchBarStyle"];

        Content = new StackLayout {
            Children = { searchBar1, searchBar2, searchBar3, searchBar4,    button    }
        };
    }
    ...
}
```

In C#, le [`SearchBar`](xref:Xamarin.Forms.SearchBar) istanze usano il [`SetDynamicResource`](xref:Xamarin.Forms.Element.SetDynamicResource*) metodo a cui fare riferimento `searchBarStyle` . Il `OnButtonClicked` codice del gestore eventi è identico all'esempio XAML e, quando viene eseguito, `searchBarStyle` passa tra `blueSearchBarStyle` e `greenSearchBarStyle` .

## <a name="dynamic-style-inheritance"></a>Ereditarietà dinamica dello stile

La derivazione di uno stile da uno stile dinamico non può essere eseguita utilizzando la [`Style.BasedOn`](xref:Xamarin.Forms.Style.BasedOn) Proprietà. Al contrario, la [`Style`](xref:Xamarin.Forms.Style) classe include la [`BaseResourceKey`](xref:Xamarin.Forms.Style.BaseResourceKey) proprietà, che può essere impostata su una chiave del dizionario il cui valore potrebbe cambiare dinamicamente.

Nell'esempio di codice seguente viene illustrata l'ereditarietà dello stile *dinamico* in una pagina XAML:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms" xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml" x:Class="Styles.DynamicStylesInheritancePage" Title="Dynamic Inheritance" IconImageSource="xaml.png">
    <ContentPage.Resources>
        <ResourceDictionary>
            <Style x:Key="baseStyle" TargetType="View">
              ...
            </Style>
            <Style x:Key="blueSearchBarStyle" TargetType="SearchBar" BasedOn="{StaticResource baseStyle}">
              ...
            </Style>
            <Style x:Key="greenSearchBarStyle" TargetType="SearchBar">
              ...
            </Style>
            <Style x:Key="tealSearchBarStyle" TargetType="SearchBar" BaseResourceKey="searchBarStyle">
              ...
            </Style>
            ...
        </ResourceDictionary>
    </ContentPage.Resources>
    <ContentPage.Content>
        <StackLayout Padding="0,20,0,0">
            <SearchBar Text="These SearchBar controls" Style="{StaticResource tealSearchBarStyle}" />
            ...
        </StackLayout>
    </ContentPage.Content>
</ContentPage>
```

Le [`SearchBar`](xref:Xamarin.Forms.SearchBar) istanze usano l' `StaticResource` estensione di markup per fare riferimento a un oggetto [`Style`](xref:Xamarin.Forms.Style) denominato `tealSearchBarStyle` . `Style`In questo modo vengono impostate alcune proprietà aggiuntive e viene utilizzata la [`BaseResourceKey`](xref:Xamarin.Forms.Style.BaseResourceKey) proprietà a cui fare riferimento `searchBarStyle` . L' `DynamicResource` estensione di markup non è necessaria perché `tealSearchBarStyle` non verrà modificata, ad eccezione dell'oggetto `Style` che deriva da. Pertanto, `tealSearchBarStyle` mantiene un collegamento a `searchBarStyle` e viene modificato quando lo stile di base viene modificato.

Nel file code-behind, il costruttore crea una [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) voce con la chiave `searchBarStyle` , in base all'esempio precedente che ha dimostrato gli stili dinamici. Quando `OnButtonClicked` viene eseguito il gestore eventi, `searchBarStyle` passa tra `blueSearchBarStyle` e `greenSearchBarStyle` . Il risultato è l'aspetto illustrato negli screenshot seguenti:

Esempio di ereditarietà dello stile dinamico [ ![ blu](dynamic-images/dynamic-style-inheritance-blue.png)](dynamic-images/dynamic-style-inheritance-blue-large.png#lightbox)esempio di 
 [ ![ ereditarietà dello stile dinamico verde](dynamic-images/dynamic-style-inheritance-green.png)](dynamic-images/dynamic-style-inheritance-green-large.png#lightbox)

Nell'esempio di codice seguente viene illustrata la pagina equivalente in C#:

```csharp
public class DynamicStylesInheritancePageCS : ContentPage
{
    bool originalStyle = true;

    public DynamicStylesInheritancePageCS ()
    {
        ...
        var baseStyle = new Style (typeof(View)) {
            ...
        };
        var blueSearchBarStyle = new Style (typeof(SearchBar)) {
            ...
        };
        var greenSearchBarStyle = new Style (typeof(SearchBar)) {
            ...
        };
        var tealSearchBarStyle = new Style (typeof(SearchBar)) {
            BaseResourceKey = "searchBarStyle",
            ...
        };
        ...
        Resources = new ResourceDictionary ();
        Resources.Add ("blueSearchBarStyle", blueSearchBarStyle);
        Resources.Add ("greenSearchBarStyle", greenSearchBarStyle);
        Resources ["searchBarStyle"] = Resources ["blueSearchBarStyle"];

        Content = new StackLayout {
            Children = {
                new SearchBar { Text = "These SearchBar controls", Style = tealSearchBarStyle },
                ...
            }
        };
    }
    ...
}
```

L'oggetto `tealSearchBarStyle` viene assegnato direttamente alla [`Style`](xref:Xamarin.Forms.NavigableElement.Style) proprietà delle [`SearchBar`](xref:Xamarin.Forms.SearchBar) istanze di. `Style`In questo modo vengono impostate alcune proprietà aggiuntive e viene utilizzata la [`BaseResourceKey`](xref:Xamarin.Forms.Style.BaseResourceKey) proprietà a cui fare riferimento `searchBarStyle` . Il [`SetDynamicResource`](xref:Xamarin.Forms.Element.SetDynamicResource*) metodo non è necessario in questo caso perché `tealSearchBarStyle` non cambia, ad eccezione del `Style` fatto che deriva da. Pertanto, `tealSearchBarStyle` mantiene un collegamento a `searchBarStyle` e viene modificato quando lo stile di base viene modificato.

## <a name="related-links"></a>Collegamenti correlati

- [Estensioni di markup XAML](~/xamarin-forms/xaml/xaml-basics/xaml-markup-extensions.md)
- [Stili dinamici (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-styles-dynamicstyles)
- [Utilizzo degli stili (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithstyles)
- [ResourceDictionary](xref:Xamarin.Forms.ResourceDictionary)
- [Style](xref:Xamarin.Forms.Style)
- [Setter](xref:Xamarin.Forms.Setter)

## <a name="related-video"></a>Video correlato

> [!Video https://channel9.msdn.com/Shows/XamarinShow/XamarinForms-101-Dynamic-Resources/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]
