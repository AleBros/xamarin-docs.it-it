---
title: Binding xamarin. Forms Basic
description: In questo articolo viene illustrato come utilizzare xamarin. Forms associazione dati, che collega una coppia di proprietà tra due oggetti, almeno uno dei quali è in genere un oggetto dell'interfaccia utente. Questi due oggetti vengono chiamati l'origine e destinazione.
ms.prod: xamarin
ms.assetid: 96553DF7-12EA-4FB2-AE85-3D1D59382B40
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/05/2018
ms.openlocfilehash: f932b7dfbcccb8f1c6ccb726f5e48c2df6e93c6c
ms.sourcegitcommit: 66682dd8e93c0e4f5dee69f32b5fc5a96443e307
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/08/2018
ms.locfileid: "35241689"
---
# <a name="xamarinforms-basic-bindings"></a>Binding xamarin. Forms Basic

Un'associazione di dati di xamarin. Forms consente di collegare una coppia di proprietà tra due oggetti, almeno uno dei quali è in genere un oggetto dell'interfaccia utente. Questi due oggetti vengono chiamati i *destinazione* e *origine*:

- Il *destinazione* sia l'oggetto (proprietà) in cui è impostato il data binding.
- Il *origine* è l'oggetto (e proprietà) a cui fa riferimento l'associazione dati.

Questa distinzione può talvolta risultare: nel caso più semplice, flussi di dati dall'origine alla destinazione, il che significa che il valore della proprietà di destinazione è impostato il valore della proprietà di origine. Tuttavia, in alcuni casi, possono in alternativa del flusso di dati dalla destinazione all'origine o in entrambe le direzioni. Per evitare confusione, tenere presente che la destinazione è sempre l'oggetto in cui l'associazione di dati viene impostato anche se è la fornitura di dati anziché di ricezione di dati.

## <a name="bindings-with-a-binding-context"></a>Associazioni con un contesto di associazione

Sebbene le associazioni dati sono in genere specificate interamente in XAML, è utile per visualizzare le associazioni dati nel codice. Il **associazione codice base** pagina contiene un file XAML con un `Label` e `Slider`:

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

Il `Slider` è impostato per un intervallo da 0 a 360. Lo scopo di questo programma è la rotazione di `Label` modificando il `Slider`.

Senza le associazioni di dati, è necessario impostare il `ValueChanged` evento del `Slider` a un gestore eventi che accede al `Value` proprietà del `Slider` e imposta tale valore il `Rotation` proprietà del `Label`. L'associazione dati consente di automatizzare il processo. il gestore dell'evento e il codice all'interno di esso non sono più necessari.

È possibile impostare un'associazione in un'istanza di qualsiasi classe che deriva da [ `BindableObject` ](https://developer.xamarin.com/api/type/Xamarin.Forms.BindableObject/), che include `Element`, `VisualElement`, `View`, e `View` derivati.  L'associazione è sempre impostato sull'oggetto di destinazione. L'associazione fa riferimento all'oggetto di origine. Per impostare l'associazione dati, utilizzare i seguenti due membri della classe di destinazione:

- Il [ `BindingContext` ](https://developer.xamarin.com/api/property/Xamarin.Forms.BindableObject.BindingContext/) proprietà specifica l'oggetto di origine.
- Il [ `SetBinding` ](https://developer.xamarin.com/api/member/Xamarin.Forms.BindableObject.SetBinding/p/Xamarin.Forms.BindableProperty/Xamarin.Forms.BindingBase/) metodo consente di specificare le proprietà di destinazione e origine.

In questo esempio, il `Label` è la destinazione dell'associazione e `Slider` è l'origine di associazione. Le modifiche di `Slider` origine interessano la rotazione del `Label` destinazione. Flussi di dati dall'origine alla destinazione.

Il `SetBinding` definito dal metodo `BindableObject` dispone di un argomento di tipo [ `BindingBase` ](https://developer.xamarin.com/api/type/Xamarin.Forms.BindingBase/) da cui il [ `Binding` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Binding/) classe deriva, ma sono presenti altri `SetBinding` metodi definito dal [ `BindableObjectExtensions` ](https://developer.xamarin.com/api/type/Xamarin.Forms.BindableObjectExtensions/) classe. Il file code-behind nel **associazione codice base** Usa un semplice esempio [ `SetBinding` ](https://developer.xamarin.com/api/member/Xamarin.Forms.BindableObjectExtensions.SetBinding/p/Xamarin.Forms.BindableObject/Xamarin.Forms.BindableProperty/System.String/) il metodo di estensione da questa classe.

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

Il `Label` oggetto è la destinazione dell'associazione in modo che l'oggetto in cui è impostata questa proprietà e su cui viene chiamato il metodo. Il `BindingContext` proprietà indica l'origine di associazione, ovvero il `Slider`.

Il `SetBinding` metodo viene chiamato su una destinazione del binding, ma specifica sia la proprietà di destinazione e la proprietà di origine. La proprietà di destinazione viene specificata come un `BindableProperty` oggetto: `Label.RotationProperty`. La proprietà di origine è specificata come stringa e indica il `Value` proprietà `Slider`.

Il `SetBinding` metodo rivela una delle regole delle associazioni dati più importanti:

*La proprietà di destinazione deve essere supportata da una proprietà associabile.*

Questa regola presuppone che l'oggetto di destinazione deve essere un'istanza di una classe che deriva da `BindableObject`. Vedere il [ **proprietà associabili** ](~/xamarin-forms/xaml/bindable-properties.md) articolo per una panoramica di proprietà associabile e oggetti associabili.

Non sussiste alcuna tale regola per la proprietà di origine, che viene specificata come stringa. Internamente, la reflection viene utilizzata per accedere alla proprietà effettiva. In questo caso specifico, tuttavia, il `Value` proprietà è supportata anche da una proprietà associabile.

Il codice può essere semplificato in qualche modo: il `RotationProperty` proprietà associabile è definita da `VisualElement`ed ereditata da `Label` e `ContentPage` , pertanto non è necessario il nome della classe nel `SetBinding` chiamare:

```csharp
label.SetBinding(RotationProperty, "Value");
```

Tuttavia, incluso il nome di classe è un buon promemoria dell'oggetto di destinazione.

Come modificare il `Slider`, `Label` ruota conseguenza:

[![Codice Basice associazione](basic-bindings-images/basiccodebinding-small.png "codice base associazione")](basic-bindings-images/basiccodebinding-large.png#lightbox "associazione di base di codice")

Il **associazione Xaml di base** è identica alla pagina **associazione di base codice** ad eccezione del fatto che definisce l'associazione di tutti i dati in XAML:

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

Solo come codice, il data binding è impostato sull'oggetto di destinazione, ovvero il `Label`. Sono coinvolti due estensioni di markup XAML. Di seguito vengono immediatamente riconoscibile dai delimitatori parentesi graffa.

- Il `x:Reference` estensione di markup è necessario fare riferimento all'oggetto di origine, ovvero il `Slider` denominato `slider`.
- Il `Binding` collegamenti estensione di markup di `Rotation` proprietà del `Label` per il `Value` proprietà del `Slider`.

Vedere l'articolo [le estensioni di Markup XAML](~/xamarin-forms/xaml/markup-extensions/index.md) per ulteriori informazioni sulle estensioni di markup XAML. Il `x:Reference` estensione di markup è supportato dal [ `ReferenceExtension` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Xaml.ReferenceExtension/) classe; `Binding` è supportato dal [ `BindingExtension` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Xaml.BindingExtension/) classe. Come il codice XML indicano i prefissi dello spazio dei nomi, `x:Reference` fa parte della specifica di XAML 2009, mentre `Binding` fa parte di xamarin. Forms. Si noti che virgolette non vengono visualizzati all'interno delle parentesi graffe.

È facile dimenticare di `x:Reference` estensione di markup quando si imposta la `BindingContext`. È comune erroneamente impostare la proprietà direttamente per il nome dell'origine di associazione simile al seguente:

```xaml
BindingContext="slider"
```

Ma che non è corretto. Imposta il markup di `BindingContext` proprietà per un `string` oggetto i cui caratteri ortografico "slider"!

Si noti che la proprietà di origine specificata con il [ `Path` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Xaml.BindingExtension.Path/) proprietà di `BindingExtension`, che corrisponde alla [ `Path` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Binding.Path/) proprietà del [ `Binding` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Binding/) classe.

Il markup visualizzato sul **associazione XAML di base** pagina può essere semplificata: le estensioni di markup XAML, ad esempio `x:Reference` e `Binding` può avere *proprietà content* definiti attributi, che per XAML le estensioni di markup significa che il nome della proprietà non deve visualizzare. Il `Name` proprietà è la proprietà content del `x:Reference`e `Path` proprietà è la proprietà content del `Binding`, il che significa che possono essere eliminate delle espressioni:

```xaml
<Label Text="TEXT"
       FontSize="80"
       HorizontalOptions="Center"
       VerticalOptions="CenterAndExpand"
       BindingContext="{x:Reference slider}"
       Rotation="{Binding Value}" />
```

## <a name="bindings-without-a-binding-context"></a>Associazioni senza un contesto di associazione

Il `BindingContext` proprietà è un importante componente delle associazioni dati, ma non è sempre necessario. L'oggetto di origine possa essere specificata nel `SetBinding` chiamare o `Binding` estensione di markup.

Questa funzionalità viene illustrata la **alternativa codice associazione** esempio. Il file XAML è simile al **associazione codice base** di esempio con la differenza che il `Slider` è definito per controllare il `Scale` proprietà del `Label`. Per questo motivo, il `Slider` è impostato per un intervallo di &ndash;2 per 2:

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

Il file code-behind imposta l'associazione con il [ `SetBinding` ](https://developer.xamarin.com/api/member/Xamarin.Forms.BindableObject.SetBinding/p/Xamarin.Forms.BindableProperty/Xamarin.Forms.BindingBase/) definito dal metodo `BindableObject`. L'argomento è un [costruttore](https://developer.xamarin.com/api/constructor/Xamarin.Forms.Binding.Binding/p/System.String/Xamarin.Forms.BindingMode/Xamarin.Forms.IValueConverter/System.Object/System.String/System.Object/) per il [ `Binding` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Binding/) classe:

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

Il `Binding` costruttore ha 6 parametri, pertanto la `source` viene specificato con un argomento denominato. L'argomento è il `slider` oggetto.

Eseguire il programma potrebbe essere leggermente sorprendente:

[![Codice alternativo associazione](basic-bindings-images/alternativecodebinding-small.png "codice alternativo associazione")](basic-bindings-images/alternativecodebinding-large.png#lightbox "associazione codice alternativo")

La schermata di iOS a sinistra mostra la schermata aspetto quando viene visualizzata la prima pagina. Dove è il `Label`?

Il problema è che il `Slider` ha un valore iniziale pari a 0. In questo modo il `Scale` proprietà del `Label` anche essere impostata su 0, si esegue l'override sul valore predefinito 1. Di conseguenza, il `Label` viene inizialmente invisibile. Come dimostrano le schermate di Android e Windows piattaforma UWP (Universal), è possibile modificare il `Slider` per rendere il `Label` verrà nuovamente visualizzato, ma la scomparsa iniziale viene aggiunto.

Si apprenderà nel [articolo successivo](binding-mode.md) come evitare questo problema inizializzando il `Slider` il valore predefinito del `Scale` proprietà.

Il **associazione XAML alternativa** pagina viene illustrata l'associazione equivalente interamente in XAML:

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

A questo punto il `Binding` estensione di markup ha due proprietà, impostare `Source` e `Path`, separati da una virgola. Essi possono essere visualizzati nella stessa riga se si preferisce:

```xaml
Scale="{Binding Source={x:Reference slider}, Path=Value}" />
```

Il `Source` proprietà è impostata a incorporato `x:Reference` estensione di markup che non ha la stessa sintassi di impostazione di `BindingContext`. Si noti che virgolette non vengono visualizzati all'interno delle parentesi graffe e che le due proprietà devono essere separate da una virgola.

La proprietà content del `Binding` estensione di markup `Path`, ma la `Path=` parte dell'estensione di markup può essere eliminata solo se è la prima proprietà nell'espressione. Per eliminare il `Path=` parte, è necessario sostituire le due proprietà:

```xaml
Scale="{Binding Value, Source={x:Reference slider}}" />
```

Anche se le estensioni di markup XAML in genere sono racchiusi tra parentesi graffe, possono anche essere espressi come elementi oggetto:

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

A questo punto il `Source` e `Path` proprietà sono attributi XAML regolari: I valori vengono visualizzati all'interno di virgolette e gli attributi non sono separati da una virgola. Il `x:Reference` estensione di markup può diventare anche un elemento dell'oggetto:

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

Questa sintassi non è comune, ma in alcuni casi è necessario quando sono coinvolti oggetti complessi.

Impostano gli esempi illustrati fino a questo punto il `BindingContext` proprietà e `Source` proprietà di `Binding` per un `x:Reference` estensione di markup per fare riferimento a un'altra visualizzazione della pagina. Queste due proprietà sono di tipo `Object`, e possono essere impostate per qualsiasi oggetto che include proprietà che sono adatte per le origini di associazione.

In anticipo gli articoli, sarà possibile osservare che è possibile impostare il `BindingContext` o `Source` proprietà per un `x:Static` estensione di markup per fare riferimento al valore di una proprietà statica o un campo o un `StaticResource` estensione di markup per fare riferimento a un oggetto archiviato in un dizionario risorse, oppure direttamente a un oggetto, che è in genere (ma non sempre) un'istanza di un elemento ViewModel.

Il `BindingContext` può anche essere impostata su un `Binding` oggetto in modo che il `Source` e `Path` le proprietà di `Binding` definire il contesto di associazione.

## <a name="binding-context-inheritance"></a>Ereditarietà del contesto di associazione

In questo articolo, si è visto che è possibile specificare l'oggetto di origine usando il `BindingContext` proprietà o `Source` proprietà del `Binding` oggetto. Se entrambi sono impostate, il `Source` proprietà del `Binding` ha la precedenza sul `BindingContext`.

Il `BindingContext` proprietà ha un'aspetto estremamente importante caratteristica:

*L'impostazione del `BindingContext` proprietà viene ereditata tramite la struttura ad albero visuale.*

Come si vedrà, può essere molto utile per semplificare le espressioni di associazione in alcuni casi &mdash; particolare in scenari Model-View-ViewModel (MVVM) &mdash; è essenziale.

Il **ereditarietà di contesto di associazione** esempio è una dimostrazione dell'ereditarietà del contesto di associazione semplice:

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

Il `BindingContext` proprietà del `StackLayout` è impostato sul `slider` oggetto. Questo contesto di associazione viene ereditato da entrambi il `Label` e `BoxView`, entrambi privi di loro `Rotation` impostate in modo che il `Value` proprietà del `Slider`:

[![Ereditarietà del contesto di associazione](basic-bindings-images/bindingcontextinheritance-small.png "ereditarietà del contesto di associazione")](basic-bindings-images/bindingcontextinheritance-large.png#lightbox "ereditarietà del contesto di associazione")

Nel [articolo successivo](binding-mode.md), si noterà come la *modalità di associazione* possibile modificare il flusso di dati tra oggetti di origine e destinazione.


## <a name="related-links"></a>Collegamenti correlati

- [Data Binding Demo (esempio)](https://developer.xamarin.com/samples/xamarin-forms/DataBindingDemos/)
- [Capitolo di associazione di dati dalla Rubrica di xamarin. Forms](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter16.md)
