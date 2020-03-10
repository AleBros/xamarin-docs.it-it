---
title: Stili dinamici in xamarin. Forms
description: Questo articolo illustra come un'applicazione xamarin. Forms possa rispondere alle modifiche di stile in modo dinamico in fase di esecuzione usando le risorse dinamiche.
ms.prod: xamarin
ms.assetid: 13D4FA4B-DF10-42BF-B001-2C49367FC216
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/28/2019
ms.custom: video
ms.openlocfilehash: 9a26532d13b843b812da94739be071c7accac212
ms.sourcegitcommit: eedc6032eb5328115cb0d99ca9c8de48be40b6fa
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/07/2020
ms.locfileid: "78918675"
---
# <a name="dynamic-styles-in-xamarinforms"></a>Stili dinamici in xamarin. Forms

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-styles-dynamicstyles)

_Gli stili non rispondono alle modifiche delle proprietà e rimangono invariati per la durata di un'applicazione. Ad esempio, dopo avere assegnato uno stile a un elemento visivo, se una delle istanze del setter viene modificata, rimossa o se è stata aggiunta una nuova istanza di setter, le modifiche non verranno applicate all'elemento visivo. Tuttavia, le applicazioni possono rispondere dinamicamente alle modifiche di stile in fase di esecuzione tramite risorse dinamiche._

L'estensione di markup `DynamicResource` è simile all'estensione di markup `StaticResource` in quanto entrambi usano una chiave del dizionario per recuperare un valore da una [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary). Tuttavia, mentre il `StaticResource` esegue una singola ricerca nel dizionario, il `DynamicResource` gestisce un collegamento alla chiave del dizionario. Pertanto, se la voce di dizionario associata alla chiave viene sostituita, la modifica viene applicata all'elemento visivo. In questo modo le modifiche di stile di runtime da eseguire in un'applicazione.

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

Le istanze di [`SearchBar`](xref:Xamarin.Forms.SearchBar) usano l'estensione di markup `DynamicResource` per fare riferimento a un [`Style`](xref:Xamarin.Forms.Style) denominato `searchBarStyle`, che non è definito in XAML. Tuttavia, poiché le proprietà [`Style`](xref:Xamarin.Forms.NavigableElement.Style) delle istanze di `SearchBar` sono impostate utilizzando un `DynamicResource`, la chiave del dizionario mancante non comporta la generazione di un'eccezione.

Al contrario, nel file code-behind, il costruttore crea una voce di [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) con la chiave `searchBarStyle`, come illustrato nell'esempio di codice seguente:

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

Quando si esegue il gestore dell'evento `OnButtonClicked`, `searchBarStyle` passa da `blueSearchBarStyle` a `greenSearchBarStyle`. Il risultato è l'aspetto illustrato negli screenshot seguenti:

esempio di [![stile dinamico blu](dynamic-images/dynamic-style-blue.png)](dynamic-images/dynamic-style-blue-large.png#lightbox)
[![esempio di stile dinamico verde](dynamic-images/dynamic-style-green.png)](dynamic-images/dynamic-style-green-large.png#lightbox)

Esempio di codice seguente illustra la pagina equivalente nel linguaggio c#:

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

In C#le istanze di [`SearchBar`](xref:Xamarin.Forms.SearchBar) usano il metodo [`SetDynamicResource`](xref:Xamarin.Forms.Element.SetDynamicResource*) per fare riferimento `searchBarStyle`. Il codice del gestore eventi `OnButtonClicked` è identico all'esempio XAML e, quando viene eseguito, `searchBarStyle` passa da `blueSearchBarStyle` a `greenSearchBarStyle`.

## <a name="dynamic-style-inheritance"></a>Ereditarietà dinamica dello stile

La derivazione di uno stile da uno stile dinamico non può essere eseguita utilizzando la proprietà [`Style.BasedOn`](xref:Xamarin.Forms.Style.BasedOn) . Al contrario, la classe [`Style`](xref:Xamarin.Forms.Style) include la proprietà [`BaseResourceKey`](xref:Xamarin.Forms.Style.BaseResourceKey) , che può essere impostata su una chiave del dizionario il cui valore potrebbe cambiare dinamicamente.

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

Le istanze di [`SearchBar`](xref:Xamarin.Forms.SearchBar) usano l'estensione di markup `StaticResource` per fare riferimento a un [`Style`](xref:Xamarin.Forms.Style) denominato `tealSearchBarStyle`. Questa `Style` imposta alcune proprietà aggiuntive e usa la proprietà [`BaseResourceKey`](xref:Xamarin.Forms.Style.BaseResourceKey) per fare riferimento `searchBarStyle`. L'estensione di markup `DynamicResource` non è necessaria perché `tealSearchBarStyle` non verrà modificata, ad eccezione del `Style` che deriva da. Pertanto, `tealSearchBarStyle` gestisce un collegamento a `searchBarStyle` e viene modificato quando lo stile di base viene modificato.

Nel file code-behind, il costruttore crea una voce di [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) con la chiave `searchBarStyle`, in base all'esempio precedente che ha dimostrato gli stili dinamici. Quando si esegue il gestore dell'evento `OnButtonClicked`, `searchBarStyle` passa da `blueSearchBarStyle` a `greenSearchBarStyle`. Il risultato è l'aspetto illustrato negli screenshot seguenti:

[![esempio di ereditarietà dello stile dinamico blu](dynamic-images/dynamic-style-inheritance-blue.png)](dynamic-images/dynamic-style-inheritance-blue-large.png#lightbox)
[![esempio di ereditarietà dello stile dinamico verde](dynamic-images/dynamic-style-inheritance-green.png)](dynamic-images/dynamic-style-inheritance-green-large.png#lightbox)

Esempio di codice seguente illustra la pagina equivalente nel linguaggio c#:

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

Il `tealSearchBarStyle` viene assegnato direttamente alla proprietà [`Style`](xref:Xamarin.Forms.NavigableElement.Style) delle istanze di [`SearchBar`](xref:Xamarin.Forms.SearchBar) . Questa `Style` imposta alcune proprietà aggiuntive e usa la proprietà [`BaseResourceKey`](xref:Xamarin.Forms.Style.BaseResourceKey) per fare riferimento `searchBarStyle`. Il metodo [`SetDynamicResource`](xref:Xamarin.Forms.Element.SetDynamicResource*) non è necessario in questo caso perché `tealSearchBarStyle` non verrà modificato, ad eccezione del `Style` che deriva da. Pertanto, `tealSearchBarStyle` gestisce un collegamento a `searchBarStyle` e viene modificato quando lo stile di base viene modificato.

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
