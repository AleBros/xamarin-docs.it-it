---
title: Stili dinamici in xamarin. Forms
description: Questo articolo illustra come un'applicazione xamarin. Forms possa rispondere alle modifiche di stile in modo dinamico in fase di esecuzione usando le risorse dinamiche.
ms.prod: xamarin
ms.assetid: 13D4FA4B-DF10-42BF-B001-2C49367FC216
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/17/2016
ms.openlocfilehash: b9ec52f65df4a21eb91a7f96f647b480bf97b33d
ms.sourcegitcommit: 482aef652bdaa440561252b6a1a1c0a40583cd32
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/21/2019
ms.locfileid: "65971157"
---
# <a name="dynamic-styles-in-xamarinforms"></a>Stili dinamici in xamarin. Forms

[![Scaricare l'esempio](~/media/shared/download.png) scaricare l'esempio](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Styles/DynamicStyles/)

_Gli stili non rispondere alle modifiche delle proprietà e rimangono invariati per la durata di un'applicazione. Dopo l'assegnazione di uno stile a un elemento visivo, se una delle istanze del Setter modificata, rimossi o una nuova istanza di Setter aggiunto, ad esempio, le modifiche non verrà applicate all'elemento visivo. Tuttavia, le applicazioni possono rispondere alle modifiche di stile in modo dinamico in fase di esecuzione usando le risorse dinamiche._

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

Quando la `OnButtonClicked` viene eseguito il gestore eventi, `searchBarStyle` passa `blueSearchBarStyle` e `greenSearchBarStyle`. Ciò comporta l'aspetto illustrato negli screenshot seguenti:

[![](dynamic-images/dynamic-style-blue.png "Blu esempio stile di visualizzazione dinamica")](dynamic-images/dynamic-style-blue-large.png#lightbox "blu esempio Style dinamica")
[![](dynamic-images/dynamic-style-green.png "verde esempio Style dinamica") ] (dynamic-images/dynamic-style-green-large.png#lightbox "Verde esempio stile di visualizzazione dinamica")

Esempio di codice seguente illustra la pagina equivalente nel linguaggio C#:

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

In C#, il [ `SearchBar` ](xref:Xamarin.Forms.SearchBar) istanze usare i [ `SetDynamicResource` ](xref:Xamarin.Forms.Element.SetDynamicResource*) metodo a cui fare riferimento `searchBarStyle`. Il `OnButtonClicked` è identico all'esempio di XAML e, quando eseguita, il codice del gestore eventi `searchBarStyle` passa `blueSearchBarStyle` e `greenSearchBarStyle`.

## <a name="dynamic-style-inheritance"></a>Ereditarietà degli stili dinamici

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

Nel file code-behind, il costruttore crea un [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary) voce con la chiave `searchBarStyle`, come indicato nell'esempio precedente che dimostrato stili dinamici. Quando la `OnButtonClicked` viene eseguito il gestore eventi, `searchBarStyle` passa `blueSearchBarStyle` e `greenSearchBarStyle`. Ciò comporta l'aspetto illustrato negli screenshot seguenti:

[![](dynamic-images/dynamic-style-inheritance-blue.png "Blu di esempio di ereditarietà di stile di visualizzazione dinamica")](dynamic-images/dynamic-style-inheritance-blue-large.png#lightbox "blu di esempio di ereditarietà di stile di visualizzazione dinamici")
[![](dynamic-images/dynamic-style-inheritance-green.png "verde stile dinamico Esempio di ereditarietà")](dynamic-images/dynamic-style-inheritance-green-large.png#lightbox "verde di esempio di ereditarietà di stile di visualizzazione dinamica")

Esempio di codice seguente illustra la pagina equivalente nel linguaggio C#:

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
- [Stili dinamici (esempio)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Styles/DynamicStyles/)
- [Utilizzo di stili (esempio)](https://developer.xamarin.com/samples/xamarin-forms/WorkingWithStyles/)
- [ResourceDictionary](xref:Xamarin.Forms.ResourceDictionary)
- [Stile di visualizzazione](xref:Xamarin.Forms.Style)
- [Setter](xref:Xamarin.Forms.Setter)
