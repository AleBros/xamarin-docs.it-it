---
title: Stili dinamici in Xamarin.Forms
description: Questo articolo illustra come un'applicazione Xamarin.Forms possa rispondere alle modifiche di stile in modo dinamico in fase di esecuzione usando le risorse dinamiche.
ms.prod: xamarin
ms.assetid: 13D4FA4B-DF10-42BF-B001-2C49367FC216
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/28/2019
ms.custom: video
ms.openlocfilehash: 9a26532d13b843b812da94739be071c7accac212
ms.sourcegitcommit: c9651cad80c2865bc628349d30e82721c01ddb4a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/03/2019
ms.locfileid: "70228198"
---
# <a name="dynamic-styles-in-xamarinforms"></a>Stili dinamici in Xamarin.Forms

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-styles-dynamicstyles)

_Gli stili non rispondono alle modifiche delle proprietà e rimangono invariati per la durata di un'applicazione. Ad esempio, dopo avere assegnato uno stile a un elemento visivo, se una delle istanze del setter viene modificata, rimossa o se è stata aggiunta una nuova istanza di setter, le modifiche non verranno applicate all'elemento visivo. Tuttavia, le applicazioni possono rispondere dinamicamente alle modifiche di stile in fase di esecuzione tramite risorse dinamiche._

Il `DynamicResource` estensione di markup è simile al `StaticResource` estensione di markup in che utilizzino entrambi una chiave del dizionario per recuperare un valore da un [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary). Tuttavia, se il `StaticResource` esegue una ricerca singolo dizionario, la `DynamicResource` mantiene un collegamento alla chiave del dizionario. Pertanto, se la voce di dizionario associata alla chiave viene sostituita, la modifica viene applicata all'elemento visivo. In questo modo le modifiche di stile di runtime da eseguire in un'applicazione.

L'esempio di codice seguente illustra *dinamica* stili in una pagina XAML:

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

Il [ `SearchBar` ](xref:Xamarin.Forms.SearchBar) istanze usare i `DynamicResource` estensione di markup per fare riferimento a una [ `Style` ](xref:Xamarin.Forms.Style) denominato `searchBarStyle`, che non è definita in XAML i. Tuttavia, perché il [ `Style` ](xref:Xamarin.Forms.NavigableElement.Style) le proprietà del `SearchBar` istanze vengono configurate utilizzando un `DynamicResource`, la chiave del dizionario mancante non genera un'eccezione generata.

Nel file code-behind, invece, il costruttore crea un [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary) voce con la chiave `searchBarStyle`, come illustrato nell'esempio di codice seguente:

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

Quando la `OnButtonClicked` viene eseguito il gestore eventi, `searchBarStyle` passa `blueSearchBarStyle` e `greenSearchBarStyle`. Il risultato è l'aspetto illustrato negli screenshot seguenti:

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

In c#, il [ `SearchBar` ](xref:Xamarin.Forms.SearchBar) istanze usare i [ `SetDynamicResource` ](xref:Xamarin.Forms.Element.SetDynamicResource*) metodo a cui fare riferimento `searchBarStyle`. Il `OnButtonClicked` è identico all'esempio di XAML e, quando eseguita, il codice del gestore eventi `searchBarStyle` passa `blueSearchBarStyle` e `greenSearchBarStyle`.

## <a name="dynamic-style-inheritance"></a>Ereditarietà dinamica dello stile

Derivazione di uno stile da uno stile dinamico non può essere ottenuta usando il [ `Style.BasedOn` ](xref:Xamarin.Forms.Style.BasedOn) proprietà. Al contrario, il [ `Style` ](xref:Xamarin.Forms.Style) classe include i [ `BaseResourceKey` ](xref:Xamarin.Forms.Style.BaseResourceKey) proprietà, che può essere impostato su una chiave del dizionario il cui valore potrebbe cambiare in modo dinamico.

L'esempio di codice seguente illustra *dinamica* applicare uno stile di ereditarietà in una pagina XAML:

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

Il [ `SearchBar` ](xref:Xamarin.Forms.SearchBar) istanze usare i `StaticResource` estensione di markup per fare riferimento a una [ `Style` ](xref:Xamarin.Forms.Style) denominato `tealSearchBarStyle`. Ciò `Style` imposta alcune proprietà aggiuntive e utilizza il [ `BaseResourceKey` ](xref:Xamarin.Forms.Style.BaseResourceKey) proprietà cui fare riferimento `searchBarStyle`. Il `DynamicResource` estensione di markup non è necessaria perché `tealSearchBarStyle` non verrà modificato, ad eccezione del `Style` deriva da. Pertanto `tealSearchBarStyle` mantiene un collegamento al `searchBarStyle` e viene modificato quando viene modificato lo stile di base.

Nel file code-behind, il costruttore crea un [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary) voce con la chiave `searchBarStyle`, come indicato nell'esempio precedente che dimostrato stili dinamici. Quando la `OnButtonClicked` viene eseguito il gestore eventi, `searchBarStyle` passa `blueSearchBarStyle` e `greenSearchBarStyle`. Il risultato è l'aspetto illustrato negli screenshot seguenti:

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

Il `tealSearchBarStyle` viene assegnato direttamente al [ `Style` ](xref:Xamarin.Forms.NavigableElement.Style) proprietà del [ `SearchBar` ](xref:Xamarin.Forms.SearchBar) istanze. Ciò `Style` Configura alcune proprietà aggiuntive e Usa le [ `BaseResourceKey` ](xref:Xamarin.Forms.Style.BaseResourceKey) proprietà cui fare riferimento `searchBarStyle`. Il [ `SetDynamicResource` ](xref:Xamarin.Forms.Element.SetDynamicResource*) metodo non è necessario in questo caso perché `tealSearchBarStyle` non cambia, tranne che per il `Style` deriva da. Pertanto `tealSearchBarStyle` mantiene un collegamento al `searchBarStyle` e viene modificato quando viene modificato lo stile di base.

## <a name="related-links"></a>Collegamenti correlati

- [Estensioni di markup XAML](~/xamarin-forms/xaml/xaml-basics/xaml-markup-extensions.md)
- [Stili dinamici (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-styles-dynamicstyles)
- [Utilizzo di stili (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithstyles)
- [ResourceDictionary](xref:Xamarin.Forms.ResourceDictionary)
- [Stile](xref:Xamarin.Forms.Style)
- [Setter](xref:Xamarin.Forms.Setter)

## <a name="related-video"></a>Video correlati

> [!Video https://channel9.msdn.com/Shows/XamarinShow/XamarinForms-101-Dynamic-Resources/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]
