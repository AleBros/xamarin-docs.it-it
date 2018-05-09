---
title: Stili dinamici
description: Stili non rispondere alle modifiche di proprietà e rimangono invariati per tutta la durata di un'applicazione. Ad esempio, dopo l'assegnazione di uno stile a un elemento visivo, se una delle istanze di Setter modificata, rimossi o aggiungere una nuova istanza di metodo di impostazione, le modifiche non applicate all'elemento visivo. Tuttavia, le applicazioni come rispondere alle modifiche di stile in modo dinamico in fase di esecuzione utilizzando le risorse dinamiche.
ms.prod: xamarin
ms.assetid: 13D4FA4B-DF10-42BF-B001-2C49367FC216
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/17/2016
ms.openlocfilehash: c484bdc90ec039a8d70209deabbe283cf7100610
ms.sourcegitcommit: e16517edcf471b53b4e347cd3fd82e485923d482
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/07/2018
---
# <a name="dynamic-styles"></a>Stili dinamici

_Stili non rispondere alle modifiche di proprietà e rimangono invariati per tutta la durata di un'applicazione. Ad esempio, dopo l'assegnazione di uno stile a un elemento visivo, se una delle istanze di Setter modificata, rimossi o aggiungere una nuova istanza di metodo di impostazione, le modifiche non applicate all'elemento visivo. Tuttavia, le applicazioni come rispondere alle modifiche di stile in modo dinamico in fase di esecuzione utilizzando le risorse dinamiche._

Il `DynamicResource` estensione di markup è simile al `StaticResource` estensione di markup in che utilizzano una chiave del dizionario per recuperare un valore da un [ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/). Tuttavia, se il `StaticResource` esegue la ricerca di un singolo dizionario, la `DynamicResource` mantiene un collegamento per la chiave del dizionario. Pertanto, se la voce di dizionario associata alla chiave viene sostituita, la modifica venga applicata all'elemento visivo. In questo modo le modifiche di stile di runtime da eseguire in un'applicazione.

L'esempio di codice seguente illustra *dinamica* stili in una pagina XAML:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms" xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml" x:Class="Styles.DynamicStylesPage" Title="Dynamic" Icon="xaml.png">
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

Il [ `SearchBar` ](https://developer.xamarin.com/api/type/Xamarin.Forms.SearchBar/) utilizzare istanze di `DynamicResource` estensione di markup per fare riferimento a un [ `Style` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Style/) denominato `searchBarStyle`, che non è definito nel codice XAML. Tuttavia, perché il [ `Style` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Style/) le proprietà del `SearchBar` istanze vengono impostate utilizzando un `DynamicResource`, la chiave del dizionario mancanti non generata un'eccezione generata.

Nel file code-behind, invece, il costruttore crea un [ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/) voce con la chiave `searchBarStyle`, come illustrato nell'esempio di codice seguente:

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

Quando il `OnButtonClicked` esecuzione del gestore eventi, `searchBarStyle` passerà tra `blueSearchBarStyle` e `greenSearchBarStyle`. Ciò comporta l'aspetto illustrato nelle schermate seguenti:

[![](dynamic-images/dynamic-style-blue.png "Blu stile dinamico esempio")](dynamic-images/dynamic-style-blue-large.png#lightbox "blu stile dinamico esempio")
[![](dynamic-images/dynamic-style-green.png "verde esempio stile dinamico") ] (dynamic-images/dynamic-style-green-large.png#lightbox "Verde esempio stile dinamico")

L'esempio di codice seguente illustra la pagina di equivalente in c#:

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
            Children = { searchBar1, searchBar2, searchBar3, searchBar4,    button  }
        };
    }
    ...
}
```

In c#, la [ `SearchBar` ](https://developer.xamarin.com/api/type/Xamarin.Forms.SearchBar/) utilizzare istanze di [ `SetDynamicResource` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Element.SetDynamicResource/) metodo per fare riferimento a `searchBarStyle`. Il `OnButtonClicked` codice del gestore eventi è identico all'esempio XAML e, quando eseguita, `searchBarStyle` passerà tra `blueSearchBarStyle` e `greenSearchBarStyle`.

<a name="dynamic-style-inheritance">

## <a name="dynamic-style-inheritance"></a>Ereditarietà degli stili dinamici

Derivazione di uno stile da uno stile dinamico non può essere realizzata utilizzando il [ `Style.BasedOn` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Style.BasedOn/) proprietà. Al contrario, il [ `Style` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Style/) classe include il [ `BaseResourceKey` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Style.BaseResourceKey/) proprietà, che può essere impostata su una chiave del dizionario il cui valore può variare in modo dinamico.

L'esempio di codice seguente illustra *dinamica* stile ereditarietà in una pagina XAML:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms" xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml" x:Class="Styles.DynamicStylesInheritancePage" Title="Dynamic Inheritance" Icon="xaml.png">
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

Il [ `SearchBar` ](https://developer.xamarin.com/api/type/Xamarin.Forms.SearchBar/) utilizzare istanze di `StaticResource` estensione di markup per fare riferimento a un [ `Style` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Style/) denominato `tealSearchBarStyle`. Questo `Style` imposta alcune proprietà aggiuntive e utilizza il [ `BaseResourceKey` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Style.BaseResourceKey/) proprietà per fare riferimento a `searchBarStyle`. Il `DynamicResource` estensione di markup non è necessaria perché `tealSearchBarStyle` non cambia, tranne che per il `Style` deriva da. Pertanto, `tealSearchBarStyle` mantiene un collegamento a `searchBarStyle` e viene modificato quando viene modificato lo stile di base.

Nel file code-behind, il costruttore crea un [ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/) voce con la chiave `searchBarStyle`secondo l'esempio precedente illustrato stili dinamici. Quando il `OnButtonClicked` esecuzione del gestore eventi, `searchBarStyle` passerà tra `blueSearchBarStyle` e `greenSearchBarStyle`. Ciò comporta l'aspetto illustrato nelle schermate seguenti:

[![](dynamic-images/dynamic-style-inheritance-blue.png "Blu stile dinamico ereditarietà esempio")](dynamic-images/dynamic-style-inheritance-blue-large.png#lightbox "blu stile dinamico ereditarietà esempio")
[![](dynamic-images/dynamic-style-inheritance-green.png "verde stile dinamico Esempio di ereditarietà")](dynamic-images/dynamic-style-inheritance-green-large.png#lightbox "verde stile dinamico di esempio di ereditarietà")

L'esempio di codice seguente illustra la pagina di equivalente in c#:

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

Il `tealSearchBarStyle` viene assegnato direttamente il [ `Style` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Style/) proprietà del [ `SearchBar` ](https://developer.xamarin.com/api/type/Xamarin.Forms.SearchBar/) istanze. Questo `Style` imposta alcune proprietà aggiuntive e utilizza il [ `BaseResourceKey` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Style.BaseResourceKey/) proprietà per fare riferimento a `searchBarStyle`. Il [ `SetDynamicResource` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Element.SetDynamicResource/) (metodo) non è necessario qui perché `tealSearchBarStyle` non cambia, tranne che per il `Style` deriva da. Pertanto, `tealSearchBarStyle` mantiene un collegamento a `searchBarStyle` e viene modificato quando viene modificato lo stile di base.

## <a name="summary"></a>Riepilogo

Stili non rispondere alle modifiche di proprietà e rimangono invariati per tutta la durata di un'applicazione. Tuttavia, le applicazioni come rispondere alle modifiche di stile in modo dinamico in fase di esecuzione utilizzando le risorse dinamiche. Inoltre, *dinamica* gli stili possono essere derivati da con le [ `BaseResourceKey` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Style.BaseResourceKey/) proprietà.



## <a name="related-links"></a>Collegamenti correlati

- [Estensioni di markup XAML](~/xamarin-forms/xaml/xaml-basics/xaml-markup-extensions.md)
- [Stili dinamici (esempio)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Styles/DynamicStyles/)
- [Utilizzo degli stili (esempio)](https://developer.xamarin.com/samples/xamarin-forms/WorkingWithStyles/)
- [ResourceDictionary](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/)
- [Stile](https://developer.xamarin.com/api/type/Xamarin.Forms.Style/)
- [Metodo set](https://developer.xamarin.com/api/type/Xamarin.Forms.Setter/)
