---
title: Xamarin.FormsAssociazioni di base
description: Questo articolo illustra come usare Xamarin.Forms Data Binding, che collega una coppia di proprietà tra due oggetti, almeno uno dei quali è in genere un oggetto dell'interfaccia utente. Questi due oggetti vengono chiamati origine e destinazione.
ms.prod: xamarin
ms.assetid: 96553DF7-12EA-4FB2-AE85-3D1D59382B40
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/22/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.custom: video
ms.openlocfilehash: c0c6bc6e1005997548952aedc09cd83a451e7caa
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/18/2020
ms.locfileid: "84133536"
---
# <a name="xamarinforms-basic-bindings"></a>Xamarin.FormsAssociazioni di base

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/databindingdemos)

Un Xamarin.Forms Data Binding collega una coppia di proprietà tra due oggetti, almeno uno dei quali è in genere un oggetto dell'interfaccia utente. Questi due oggetti vengono chiamati *destinazione* e *origine*:

- La *destinazione* è l'oggetto (e la proprietà) su cui è impostato il data binding.
- L'*origine* è l'oggetto (e la proprietà) a cui fa riferimento il data binding.

In certi casi questa distinzione può generare confusione. Nel caso più semplice, i dati passano dall'origine alla destinazione. Ciò significa che il valore della proprietà di destinazione è impostato dal valore della proprietà di origine. Tuttavia in alcuni casi i dati possono passare dalla destinazione all'origine o in entrambe le direzioni. Per evitare confusione, tenere presente che la destinazione è sempre l'oggetto sul quale viene impostato il data binding, anche se trasmette dati anziché riceverli.

## <a name="bindings-with-a-binding-context"></a>Binding con un contesto di binding

In genere i data binding sono specificati interamente in XAML, ma è istruttivo vederli anche sotto forma di codice. La pagina **Basic Code Binding** (Binding di codice di base) contiene un file XAML con un elemento `Label` e un elemento `Slider`:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="DataBindingDemos.BasicCodeBindingPage"
             Title="Basic Code Binding">
    <StackLayout Padding="10, 0">
        <Label x:Name="label"
               Text="TEXT"
               FontSize="48"
               HorizontalOptions="Center"
               VerticalOptions="CenterAndExpand" />

        <Slider x:Name="slider"
                Maximum="360"
                VerticalOptions="CenterAndExpand" />
    </StackLayout>
</ContentPage>
```

L'elemento `Slider` è impostato per un intervallo compreso tra 0 e 360. La finalità di questo programma è la rotazione di `Label` tramite l'azionamento di `Slider`.

Senza i data binding sarebbe necessario impostare l'evento `ValueChanged` di `Slider` su un gestore eventi che accede alla proprietà `Value` di `Slider` e imposta tale valore sulla proprietà `Rotation` di `Label`. Il data binding consente di automatizzare questo processo: il gestore eventi e il codice al suo interno non sono più necessari.

È possibile impostare un'associazione in un'istanza di qualsiasi classe che deriva da [`BindableObject`](xref:Xamarin.Forms.BindableObject) , che include i `Element` `VisualElement` `View` `View` derivati,, e.  Il binding è sempre impostato sull'oggetto di destinazione. Il binding fa riferimento all'oggetto di origine. Per impostare il data binding usare i due membri seguenti della classe di destinazione:

- La [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) proprietà specifica l'oggetto di origine.
- [ `SetBinding` ] (Xrif: Xamarin.Forms . BindableObject. SetBinding ( Xamarin.Forms . BindableProperty, Xamarin.Forms . BindingBase)) consente di specificare la proprietà di destinazione e la proprietà di origine.

In questo esempio, `Label` è la destinazione del binding e `Slider` è l'origine del binding. Le modifiche nell'origine `Slider` hanno effetto sulla rotazione della destinazione `Label`. I dati passano dall'origine alla destinazione.

Il `SetBinding` metodo definito da `BindableObject` dispone di un argomento di tipo [`BindingBase`](xref:Xamarin.Forms.BindingBase) da cui [`Binding`](xref:Xamarin.Forms.Binding) deriva la classe, ma sono presenti altri `SetBinding` metodi definiti dalla [`BindableObjectExtensions`](xref:Xamarin.Forms.BindableObjectExtensions) classe. Il file code-behind nell'esempio **Basic Code Binding** (Binding di codice di base) usa un metodo di estensione [`SetBinding`](xref:Xamarin.Forms.BindableObjectExtensions.SetBinding*) più semplice di questa classe.

```csharp
public partial class BasicCodeBindingPage : ContentPage
{
    public BasicCodeBindingPage()
    {
        InitializeComponent();

        label.BindingContext = slider;
        label.SetBinding(Label.RotationProperty, "Value");
    }
}
```

L'oggetto `Label` è la destinazione del binding, ovvero l'oggetto sul quale è impostata questa proprietà e sul quale viene chiamato il metodo. La proprietà `BindingContext` indica l'origine del binding, ovvero `Slider`.

Il metodo `SetBinding` viene chiamato sulla destinazione del binding, ma specifica sia la proprietà di destinazione che la proprietà di origine. La proprietà di destinazione viene specificata come oggetto `BindableProperty`: `Label.RotationProperty`. La proprietà di origine viene specificata come stringa e indica la proprietà `Value` di `Slider`.

Il metodo `SetBinding` rivela una delle regole più importanti per i data binding:

*La proprietà di destinazione deve essere supportata da una proprietà con binding.*

Questa regola implica che l'oggetto di destinazione deve essere un'istanza di una classe che deriva da `BindableObject`. Vedere l'articolo [**Bindable Properties**](~/xamarin-forms/xaml/bindable-properties.md) (Proprietà con binding) per una panoramica degli oggetti e delle proprietà associabili.

Non è presente una regola di questo tipo per la proprietà di origine, che viene specificata sotto forma di stringa. A livello interno, per l'accesso alla proprietà viene usata una reflection. In questo caso specifico, tuttavia, la proprietà `Value` è anche supportata da una proprietà con binding.

Il codice può essere semplificato leggermente: la proprietà con binding `RotationProperty` è definita da `VisualElement` e anche ereditata da `Label` e `ContentPage`, pertanto il nome della classe non è obbligatorio nella chiamata `SetBinding`:

```csharp
label.SetBinding(RotationProperty, "Value");
```

Tuttavia l'inclusione del nome della classe è un buon promemoria dell'oggetto di destinazione.

Quando si aziona l'elemento `Slider`, l'elemento `Label` ruota di conseguenza:

[![Associazione di codice di base](basic-bindings-images/basiccodebinding-small.png "Associazione di codice di base")](basic-bindings-images/basiccodebinding-large.png#lightbox "Associazione di codice di base")

La pagina **Basic Xaml Binding** (Binding XAML di base) è identica alla pagina **Basic Code Binding** (Binding di codice di base) salvo per il fatto che definisce l'intero data binding in XAML:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="DataBindingDemos.BasicXamlBindingPage"
             Title="Basic XAML Binding">
    <StackLayout Padding="10, 0">
        <Label Text="TEXT"
               FontSize="80"
               HorizontalOptions="Center"
               VerticalOptions="CenterAndExpand"
               BindingContext="{x:Reference Name=slider}"
               Rotation="{Binding Path=Value}" />

        <Slider x:Name="slider"
                Maximum="360"
                VerticalOptions="CenterAndExpand" />
    </StackLayout>
</ContentPage>
```

Come nel codice, il data binding è impostato sull'oggetto di destinazione, ovvero su `Label`. Sono presenti due estensioni di markup XAML. Le estensioni sono immediatamente riconoscibili per i delimitatori parentesi graffa:

- L'estensione di markup `x:Reference` è obbligatoria per il riferimento all'oggetto di origine, ovvero l'elemento `Slider` con nome `slider`.
- L'estensione di markup `Binding` collega la proprietà `Rotation` di `Label` alla proprietà `Value` di `Slider`.

Per altre informazioni sulle estensioni di markup XAML, vedere l'articolo [Estensioni di markup XAML](~/xamarin-forms/xaml/markup-extensions/index.md). L' `x:Reference` estensione di markup è supportata dalla [`ReferenceExtension`](xref:Xamarin.Forms.Xaml.ReferenceExtension) classe `Binding` . è supportata dalla [`BindingExtension`](xref:Xamarin.Forms.Xaml.BindingExtension) classe. Poiché i prefissi degli spazi dei nomi XML indicano, `x:Reference` fa parte della specifica XAML 2009, mentre `Binding` fa parte di Xamarin.Forms . Si noti che all'interno delle parentesi graffe non appaiono virgolette.

È facile dimenticare l'estensione di markup `x:Reference` durante l'impostazione di `BindingContext`. Inoltre spesso si imposta erroneamente la proprietà sul nome dell'origine di binding, come visualizzato di seguito:

```xaml
BindingContext="slider"
```

Questa prassi non è corretta. Questo markup imposta la proprietà `BindingContext` su un oggetto `string` i cui caratteri visualizzano "slider".

Si noti che la proprietà Source viene specificata con la [`Path`](xref:Xamarin.Forms.Xaml.BindingExtension.Path) proprietà di `BindingExtension` , che corrisponde alla [`Path`](xref:Xamarin.Forms.Binding.Path) proprietà della [`Binding`](xref:Xamarin.Forms.Binding) classe.

Il markup visualizzato nella pagina **Basic XAML Binding** (Binding XAML di base) può essere semplificato: le estensioni di markup XAML come `x:Reference` e `Binding` possono avere attributi *content property* definiti. Per le estensioni di markup XAML ciò significa che non è necessario che sia visualizzato il nome della proprietà. La proprietà `Name` è la proprietà di contenuto di `x:Reference`, mentre la proprietà `Path` è la proprietà di contenuto di `Binding`. Questo significa che entrambe possono essere eliminate delle espressioni:

```xaml
<Label Text="TEXT"
       FontSize="80"
       HorizontalOptions="Center"
       VerticalOptions="CenterAndExpand"
       BindingContext="{x:Reference slider}"
       Rotation="{Binding Value}" />
```

## <a name="bindings-without-a-binding-context"></a>Binding senza un contesto di binding

La proprietà `BindingContext` è un componente importante per i data binding, ma non è sempre necessaria. In alternativa l'oggetto di origine può essere specificato nella chiamata `SetBinding` o nell'estensione di markup `Binding`.

Questo è dimostrato nell'esempio **Alternative Code Binding** (Binding di codice alternativo). Il file XAML è simile a quello dell'esempio **Basic Code Binding** (Binding di codice di base), con la differenza che `Slider` viene definito per il controllo della proprietà `Scale` di `Label`. Per tale motivo `Slider` è impostato per un intervallo compreso tra &ndash;2 e 2:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="DataBindingDemos.AlternativeCodeBindingPage"
             Title="Alternative Code Binding">
    <StackLayout Padding="10, 0">
        <Label x:Name="label"
               Text="TEXT"
               FontSize="40"
               HorizontalOptions="Center"
               VerticalOptions="CenterAndExpand" />

        <Slider x:Name="slider"
                Minimum="-2"
                Maximum="2"
                VerticalOptions="CenterAndExpand" />
    </StackLayout>
</ContentPage>
```

Il file code-behind imposta l'associazione con [ `SetBinding` ] (xrif: Xamarin.Forms . BindableObject. SetBinding ( Xamarin.Forms . BindableProperty, Xamarin.Forms . BindingBase)) definito da `BindableObject` . L'argomento è un [Costruttore] (xrif: Xamarin.Forms . Binding .% 23ctor (System. String, Xamarin.Forms . BindingMode, Xamarin.Forms . IValueConverter, System. Object, System. String, System. Object)) per la [`Binding`](xref:Xamarin.Forms.Binding) classe:

```csharp
public partial class AlternativeCodeBindingPage : ContentPage
{
    public AlternativeCodeBindingPage()
    {
        InitializeComponent();

        label.SetBinding(Label.ScaleProperty, new Binding("Value", source: slider));
    }
}
```

Il costruttore `Binding` ha 6 parametri, pertanto il parametro `source` è specificato con un argomento denominato. L'argomento è l'oggetto `slider`.

L'esecuzione del programma può dare risultati inattesi:

[![Associazione di codice alternativa](basic-bindings-images/alternativecodebinding-small.png "Associazione di codice alternativa")](basic-bindings-images/alternativecodebinding-large.png#lightbox "Associazione di codice alternativa")

La schermata iOS a sinistra visualizza l'aspetto della schermata quando la pagina viene visualizzata per la prima volta. Dove si trova `Label`?

Il problema è che `Slider` ha un valore iniziale pari a 0. Di conseguenza anche la proprietà `Scale` di `Label` è impostata su 0, e questo sovrascrive il valore predefinito 1. Di conseguenza, inizialmente `Label` non è visibile. Come dimostra lo screenshot di Android, è possibile modificare l'oggetto `Slider` affinché venga `Label` nuovamente visualizzato, ma la sua scomparsa iniziale è la disconcerting.

L'[articolo successivo](binding-mode.md) illustra come evitare questo problema inizializzando `Slider` in base al valore predefinito della proprietà `Scale`.

> [!NOTE]
> La [`VisualElement`](xref:Xamarin.Forms.VisualElement) classe definisce inoltre [`ScaleX`](xref:Xamarin.Forms.VisualElement.ScaleX) le [`ScaleY`](xref:Xamarin.Forms.VisualElement.ScaleY) proprietà e, che possono ridimensionare `VisualElement` in modo diverso nelle direzioni orizzontale e verticale.

La pagina **Alternative XAML Binding** (Binding XAML alternativo) illustra il binding equivalente interamente in XAML:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="DataBindingDemos.AlternativeXamlBindingPage"
             Title="Alternative XAML Binding">
    <StackLayout Padding="10, 0">
        <Label Text="TEXT"
               FontSize="40"
               HorizontalOptions="Center"
               VerticalOptions="CenterAndExpand"
               Scale="{Binding Source={x:Reference slider},
                               Path=Value}" />

        <Slider x:Name="slider"
                Minimum="-2"
                Maximum="2"
                VerticalOptions="CenterAndExpand" />
    </StackLayout>
</ContentPage>
```

A questo punto l'estensione di markup `Binding` ha due proprietà impostate, `Source` e `Path`, separate da una virgola. Se si preferisce è possibile visualizzarle sulla stessa riga:

```xaml
Scale="{Binding Source={x:Reference slider}, Path=Value}" />
```

La proprietà `Source` è impostata su un'estensione di markup `x:Reference` incorporata, che per il resto ha la stessa sintassi dell'impostazione di `BindingContext`. Si noti che tra le parentesi graffe non vengono visualizzate virgolette e che le due proprietà devono essere separate da virgole.

La proprietà del contenuto dell'estensione di markup `Binding` è `Path`, ma la parte `Path=` dell'estensione di markup può essere eliminata solo se è la prima proprietà nell'espressione. Per eliminare la parte `Path=` è necessario scambiare le due proprietà:

```xaml
Scale="{Binding Value, Source={x:Reference slider}}" />
```

Le estensioni di markup XAML sono in genere delimitate da parentesi graffe, ma possono anche essere espresse come elementi oggetto:

```xaml
<Label Text="TEXT"
       FontSize="40"
       HorizontalOptions="Center"
       VerticalOptions="CenterAndExpand">
    <Label.Scale>
        <Binding Source="{x:Reference slider}"
                 Path="Value" />
    </Label.Scale>
</Label>
```

Ora le proprietà `Source` e `Path` sono attributi XAML normali: i valori vengono visualizzati all'interno di virgolette e gli attributi non sono separati da una virgola. Anche l'estensione di markup `x:Reference` può trasformarsi in un elemento oggetto:

```xaml
<Label Text="TEXT"
       FontSize="40"
       HorizontalOptions="Center"
       VerticalOptions="CenterAndExpand">
    <Label.Scale>
        <Binding Path="Value">
            <Binding.Source>
                <x:Reference Name="slider" />
            </Binding.Source>
        </Binding>
    </Label.Scale>
</Label>
```

Questa sintassi non è comune, ma può risultare necessaria in presenza di oggetti complessi.

Gli esempi illustrati finora impostano la proprietà `BindingContext` e la proprietà `Source` di `Binding` su un'estensione di markup `x:Reference` per fare riferimento a un'altra visualizzazione nella pagina. Queste due proprietà sono di tipo `Object` e possono essere impostate su qualsiasi oggetto che include proprietà adatte alle origini di binding.

Negli articoli successivi si apprenderà che è possibile impostare la proprietà `BindingContext` o `Source` su un'estensione di markup `x:Static` per fare riferimento al valore di una proprietà o un campo statico, oppure su un'estensione di markup `StaticResource` per fare riferimento a un oggetto archiviato in un dizionario risorse, o ancora direttamente su un oggetto, che è in genere (ma non sempre) un'istanza di un elemento ViewModel.

La proprietà `BindingContext` può anche essere impostata su un oggetto `Binding` in modo che le proprietà `Source` e `Path` di `Binding` definiscano il contesto di binding.

## <a name="binding-context-inheritance"></a>Ereditarietà del contesto di binding

In questo articolo si è visto che è possibile specificare l'oggetto di origine usando la proprietà `BindingContext` o la proprietà `Source` dell'oggetto `Binding`. Se sono impostate entrambe, la proprietà `Source` di `Binding` ha la precedenza su `BindingContext`.

La proprietà `BindingContext` presenta una caratteristica molto importante:

*L'impostazione della proprietà `BindingContext` viene ereditata nell'intera la struttura ad albero visuale.*

Come si vedrà, questa caratteristica può risultare molto utile per semplificare le espressioni di binding e in alcuni casi, soprattutto negli scenari Model-View-ViewModel (MVVM), è essenziale.

L'esempio **Binding Context Inheritance** (Ereditarietà del contesto di binding) è una dimostrazione semplice dell'ereditarietà del contesto di binding:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="DataBindingDemos.BindingContextInheritancePage"
             Title="BindingContext Inheritance">
    <StackLayout Padding="10">

        <StackLayout VerticalOptions="FillAndExpand"
                     BindingContext="{x:Reference slider}">

            <Label Text="TEXT"
                   FontSize="80"
                   HorizontalOptions="Center"
                   VerticalOptions="EndAndExpand"
                   Rotation="{Binding Value}" />

            <BoxView Color="#800000FF"
                     WidthRequest="180"
                     HeightRequest="40"
                     HorizontalOptions="Center"
                     VerticalOptions="StartAndExpand"
                     Rotation="{Binding Value}" />
        </StackLayout>

        <Slider x:Name="slider"
                Maximum="360" />

    </StackLayout>
</ContentPage>
```

La proprietà `BindingContext` di `StackLayout` è impostata sull'oggetto `slider`. Questo contesto di binding viene ereditato sia da `Label` che da `BoxView` e le proprietà `Rotation` di entrambi sono impostate sulla proprietà `Value` di `Slider`:

[![Ereditarietà del contesto di binding](basic-bindings-images/bindingcontextinheritance-small.png "Ereditarietà del contesto di binding")](basic-bindings-images/bindingcontextinheritance-large.png#lightbox "Ereditarietà del contesto di binding")

Nell'[articolo seguente](binding-mode.md) si vedrà come la *modalità di binding* può modificare il flusso di dati tra gli oggetti di origine e destinazione.

## <a name="related-links"></a>Collegamenti correlati

- [Data Binding Demos (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/databindingdemos) (Demo di data binding)
- [Capitolo di data binding da Xamarin.Forms book](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter16.md)

## <a name="related-video"></a>Video correlato

> [!Video https://channel9.msdn.com/Shows/XamarinShow/XamarinForms-101-Data-Binding/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]
