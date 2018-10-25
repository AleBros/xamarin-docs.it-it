---
title: Associazioni di base di xamarin. Forms
description: Questo articolo illustra come usare xamarin. Forms associazione dati, che collega una coppia di proprietà tra due oggetti, almeno uno dei quali è in genere un oggetto dell'interfaccia utente. Questi due oggetti vengono chiamati l'origine e destinazione.
ms.prod: xamarin
ms.assetid: 96553DF7-12EA-4FB2-AE85-3D1D59382B40
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/05/2018
ms.openlocfilehash: b52f249b184d49731fd5decdb5877c70e29a3b84
ms.sourcegitcommit: 7f6127c2f425fadc675b77d14de7a36103cff675
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/24/2018
ms.locfileid: "38998072"
---
# <a name="xamarinforms-basic-bindings"></a>Associazioni di base di xamarin. Forms

Un data binding di xamarin. Forms collega una coppia di proprietà tra due oggetti, almeno uno dei quali è in genere un oggetto dell'interfaccia utente. Questi due oggetti vengono chiamati i *destinazione* e il *origine*:

- Il *destinazione* è l'oggetto (e proprietà) in cui viene impostata l'associazione dati.
- Il *origine* sia l'oggetto (proprietà) fa riferimento il data binding.

Questa distinzione può rivelarsi un po' di confusione: nel caso più semplice, flussi di dati dall'origine alla destinazione, il che significa che il valore della proprietà di destinazione viene impostato dal valore della proprietà di origine. Tuttavia, in alcuni casi, possono in alternativa del flusso di dati dalla destinazione all'origine o in entrambe le direzioni. Per evitare confusione, tenere presente che la destinazione è sempre l'oggetto in cui viene impostata l'associazione di dati anche se è la fornitura di dati piuttosto che di ricezione di dati.

## <a name="bindings-with-a-binding-context"></a>Associazioni con un contesto di associazione

Sebbene le associazioni dati sono in genere specificate interamente in XAML, è istruttivo osservare le associazioni dati nel codice. Il **base codice associazione** pagina contiene un file XAML con un `Label` e un `Slider`:

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

Il `Slider` è impostato per un intervallo di 0 e 360. Lo scopo di questo programma consiste nel ruotare il `Label` modificando il `Slider`.

Senza le associazioni dati, è necessario impostare il `ValueChanged` eventi del `Slider` a un gestore eventi che accede al `Value` proprietà del `Slider` e imposta tale valore il `Rotation` proprietà del `Label`. Il data binding consente di automatizzare questo processo: il gestore dell'evento e il codice all'interno di esso non sono più necessari.

È possibile impostare un'associazione in un'istanza di qualsiasi classe che deriva da [ `BindableObject` ](xref:Xamarin.Forms.BindableObject), che include `Element`, `VisualElement`, `View`, e `View` derivati.  L'associazione è sempre impostato sull'oggetto di destinazione. L'associazione fa riferimento all'oggetto di origine. Per impostare l'associazione dati, utilizzare i seguenti due membri della classe di destinazione:

- Il [ `BindingContext` ](xref:Xamarin.Forms.BindableObject.BindingContext) proprietà specifica l'oggetto di origine.
- Il [ `SetBinding` ](xref:Xamarin.Forms.BindableObject.SetBinding(Xamarin.Forms.BindableProperty,Xamarin.Forms.BindingBase)) metodo consente di specificare le proprietà di destinazione e origine.

In questo esempio, il `Label` è la destinazione di associazione e il `Slider` rappresentano l'origine del binding. Modifica nel `Slider` origine interessano la rotazione del `Label` destinazione. Flussi di dati dall'origine alla destinazione.

Il `SetBinding` definito dal metodo `BindableObject` dispone di un argomento di tipo [ `BindingBase` ](xref:Xamarin.Forms.BindingBase) da cui il [ `Binding` ](xref:Xamarin.Forms.Binding) classe deriva, ma sono presenti altri `SetBinding` metodi definita per il [ `BindableObjectExtensions` ](xref:Xamarin.Forms.BindableObjectExtensions) classe. Il file code-behind nel **base codice associazione** esempio Usa un percorso più semplice [ `SetBinding` ](xref:Xamarin.Forms.BindableObjectExtensions.SetBinding*) metodo di estensione da questa classe.

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

Il `Label` oggetto è la destinazione del binding in modo che l'oggetto sul quale questa proprietà è impostata e sul quale viene chiamato il metodo. Il `BindingContext` proprietà indica l'origine dell'associazione, ovvero il `Slider`.

Il `SetBinding` metodo viene chiamato sulla destinazione del binding, ma specifica sia la proprietà di destinazione e la proprietà di origine. La proprietà di destinazione viene specificata come un `BindableProperty` oggetti: `Label.RotationProperty`. La proprietà di origine viene specificata sotto forma di stringa e indica la `Value` proprietà di `Slider`.

Il `SetBinding` metodo rivela una delle regole di associazioni dati più importanti:

*La proprietà di destinazione deve essere supportata da una proprietà associabile.*

Questa regola implica che l'oggetto di destinazione deve essere un'istanza di una classe che deriva da `BindableObject`. Vedere le [ **proprietà associabili** ](~/xamarin-forms/xaml/bindable-properties.md) articolo per una panoramica degli oggetti associabili e le proprietà associabili.

Non sono presenti tali regole per la proprietà di origine, che viene specificata sotto forma di stringa. Internamente, la reflection consente di accedere alla proprietà effettiva. In questo caso specifico, tuttavia, il `Value` proprietà è supportata anche da una proprietà associabile.

Il codice può essere semplificato leggermente: il `RotationProperty` proprietà associabili è definita dal `VisualElement`ed ereditata da `Label` e `ContentPage` anche, pertanto, il nome della classe non è obbligatorio nel `SetBinding` chiamare:

```csharp
label.SetBinding(RotationProperty, "Value");
```

Tuttavia, tra cui il nome della classe è un buon promemoria dell'oggetto di destinazione.

Come modificano le `Slider`, il `Label` ruota conseguenza:

[![Codice Basice Binding](basic-bindings-images/basiccodebinding-small.png "associazione di base del codice")](basic-bindings-images/basiccodebinding-large.png#lightbox "associazione di base del codice")

Il **associazione di base Xaml** è identica alla pagina **associazione di base codice** ad eccezione del fatto che definisce l'associazione di tutti i dati in XAML:

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

Proprio come illustrato nel codice, il data binding è impostato sull'oggetto di destinazione, ovvero il `Label`. Sono coinvolti due estensioni di markup XAML. Questi sono immediatamente riconoscibili dai delimitatori parentesi graffe:

- Il `x:Reference` estensione di markup deve fare riferimento all'oggetto di origine, ovvero il `Slider` denominata `slider`.
- Il `Binding` i collegamenti di estensione di markup il `Rotation` proprietà delle `Label` per il `Value` proprietà del `Slider`.

Vedere l'articolo [estensioni di Markup XAML](~/xamarin-forms/xaml/markup-extensions/index.md) per altre informazioni sulle estensioni di markup XAML. Il `x:Reference` estensione di markup è supportato per il [ `ReferenceExtension` ](xref:Xamarin.Forms.Xaml.ReferenceExtension) classe; `Binding` è supportata per il [ `BindingExtension` ](xref:Xamarin.Forms.Xaml.BindingExtension) classe. Come il codice XML indicano i prefissi dello spazio dei nomi, `x:Reference` fa parte della specifica di XAML 2009, mentre `Binding` fa parte di xamarin. Forms. Si noti che non racchiusi tra virgolette vengono visualizzati tra parentesi graffe.

È facile dimenticare la `x:Reference` estensione di markup durante l'impostazione di `BindingContext`. È comune erroneamente impostare la proprietà direttamente con il nome dell'origine del binding simile al seguente:

```xaml
BindingContext="slider"
```

Ma che non è corretto. Markup imposta il `BindingContext` proprietà su un `string` oggetto cui caratteri di controllo ortografico "slider".

Si noti che la proprietà di origine è specificata con il [ `Path` ](xref:Xamarin.Forms.Xaml.BindingExtension.Path) proprietà della `BindingExtension`, che corrisponde alla [ `Path` ](xref:Xamarin.Forms.Binding.Path) proprietà del [ `Binding` ](xref:Xamarin.Forms.Binding) classe.

Il markup visualizzato nel **associazione XAML di base** pagina può essere semplificata: le estensioni di markup XAML, ad esempio `x:Reference` e `Binding` può avere *contenuto proprietà* definiti attributi, che per XAML le estensioni di markup significa che non è necessario che il nome della proprietà da visualizzare. Il `Name` è la proprietà di contenuto `x:Reference`e il `Path` è la proprietà content di `Binding`, il che significa che possono essere eliminate delle espressioni:

```xaml
<Label Text="TEXT"
       FontSize="80"
       HorizontalOptions="Center"
       VerticalOptions="CenterAndExpand"
       BindingContext="{x:Reference slider}"
       Rotation="{Binding Value}" />
```

## <a name="bindings-without-a-binding-context"></a>Binding senza un contesto di associazione

Il `BindingContext` proprietà è un componente importante del data binding, ma non è sempre necessario. L'oggetto di origine invece può essere specificato nel `SetBinding` chiamare o `Binding` estensione di markup.

Ciò è dimostrato nel **alternativa codice associazione** esempio. Il file XAML è simile al **base codice associazione** di esempio con la differenza che le `Slider` viene definito al controllo il `Scale` proprietà del `Label`. Per questo motivo, il `Slider` è impostato per un intervallo di &ndash;2 di 2:

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

Il file code-behind imposta l'associazione con il [ `SetBinding` ](xref:Xamarin.Forms.BindableObject.SetBinding(Xamarin.Forms.BindableProperty,Xamarin.Forms.BindingBase)) definito dal metodo `BindableObject`. L'argomento è una [costruttore](xref:Xamarin.Forms.Binding.%23ctor(System.String,Xamarin.Forms.BindingMode,Xamarin.Forms.IValueConverter,System.Object,System.String,System.Object)) per il [ `Binding` ](xref:Xamarin.Forms.Binding) classe:

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

Il `Binding` costruttore ha 6 parametri, pertanto la `source` parametro è specificato con un argomento denominato. L'argomento è il `slider` oggetto.

Eseguire il programma potrebbe essere un po' sorprendente:

[![Associazione di codice alternativo](basic-bindings-images/alternativecodebinding-small.png "associazione codice alternativo")](basic-bindings-images/alternativecodebinding-large.png#lightbox "associazione codice alternativo")

La schermata iOS a sinistra mostra la schermata si presenta come quando viene visualizzata la pagina. Dove è il `Label`?

Il problema è che il `Slider` ha un valore iniziale pari a 0. In questo modo, il `Scale` proprietà del `Label` anche essere impostata su 0, si esegue l'override sul valore predefinito 1. Di conseguenza il `Label` viene inizialmente invisibile. Come illustrano gli screenshot di Android e Universal Windows Platform (UWP), è possibile modificare il `Slider` per rendere il `Label` verrà nuovamente visualizzato, ma la scomparsa iniziale viene aggiunto.

Scoprirai nel [prossimo articolo](binding-mode.md) come evitare questo problema inizializzando il `Slider` rispetto al valore predefinito del `Scale` proprietà.

> [!NOTE]
> Il [ `VisualElement` ](xref:Xamarin.Forms.VisualElement) classe definisce anche [ `ScaleX` ](xref:Xamarin.Forms.VisualElement.ScaleX) e [ `ScaleY` ](xref:Xamarin.Forms.VisualElement.ScaleY) proprietà, che è possibile ridimensionare il `VisualElement` in modo diverso nel direzioni orizzontale e verticale.

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

Il `Source` viene impostata a un embedded `x:Reference` estensione di markup che in caso contrario ha la stessa sintassi come impostazione di `BindingContext`. Si noti che non racchiusi tra virgolette vengono visualizzati tra parentesi graffe e che le due proprietà devono essere separate da una virgola.

La proprietà content del `Binding` estensione di markup `Path`, ma il `Path=` parte dell'estensione di markup può essere eliminata solo se è la prima proprietà nell'espressione. Per eliminare il `Path=` parte, è necessario scambiare le due proprietà:

```xaml
Scale="{Binding Value, Source={x:Reference slider}}" />
```

Anche se le estensioni di markup XAML vengono in genere delimitate da parentesi graffe, possono anche essere espressi come elementi oggetto:

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

A questo punto il `Source` e `Path` proprietà sono attributi XAML regolari: I valori vengono visualizzati all'interno di virgolette e gli attributi non sono separati da una virgola. Il `x:Reference` estensione di markup può trasformarsi in un elemento oggetto:

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

Questa sintassi non è comune, ma in alcuni casi è necessario quando sono coinvolte oggetti complessi.

Gli esempi illustrati finora impostano il `BindingContext` proprietà e il `Source` proprietà di `Binding` a un `x:Reference` estensione di markup per fare riferimento a un'altra visualizzazione nella pagina. Queste due proprietà sono di tipo `Object`, e possono essere impostate per qualsiasi oggetto che include proprietà che sono adatte per le origini di associazione.

Negli articoli-ahead, si scoprirà che è possibile impostare il `BindingContext` o `Source` proprietà di un `x:Static` estensione di markup per fare riferimento al valore di una proprietà statica o un campo, o un `StaticResource` estensione di markup per fare riferimento a un oggetto archiviato in un dizionario risorse, o direttamente a un oggetto, che è in genere (ma non sempre) un'istanza di un elemento ViewModel.

Il `BindingContext` può anche essere impostata su una `Binding` oggetto in modo che le `Source` e `Path` le proprietà di `Binding` definiscono il contesto di associazione.

## <a name="binding-context-inheritance"></a>Ereditarietà del contesto di associazione

In questo articolo, si è visto che è possibile specificare l'oggetto di origine usando il `BindingContext` proprietà o il `Source` proprietà del `Binding` oggetto. Se entrambi sono impostate, il `Source` proprietà del `Binding` ha la precedenza sul `BindingContext`.

Il `BindingContext` proprietà presenta una caratteristica molto importante:

*L'impostazione di `BindingContext` proprietà viene ereditata tramite la struttura ad albero visuale.*

Come si vedrà, può essere molto utile per semplificare le espressioni di associazione e in alcuni casi &mdash; soprattutto negli scenari Model-View-ViewModel (MVVM) &mdash; è essenziale.

Il **ereditarietà del contesto dell'associazione** esempio è una semplice dimostrazione dell'ereditarietà del contesto di associazione:

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

Il `BindingContext` proprietà del `StackLayout` è impostata sul `slider` oggetto. Il contesto di associazione viene ereditato da entrambi i `Label` e il `BoxView`, entrambi privi di loro `Rotation` impostate in modo che il `Value` proprietà del `Slider`:

[![Ereditarietà del contesto di associazione](basic-bindings-images/bindingcontextinheritance-small.png "ereditarietà del contesto di associazione")](basic-bindings-images/bindingcontextinheritance-large.png#lightbox "ereditarietà del contesto di associazione")

Nel [prossimo articolo](binding-mode.md), si vedrà come il *modalità di associazione* può modificare il flusso di dati tra gli oggetti di origine e destinazione.


## <a name="related-links"></a>Collegamenti correlati

- [Data Binding Demo (esempio)](https://developer.xamarin.com/samples/xamarin-forms/DataBindingDemos/)
- [Capitolo di associazione di dati dal libro di xamarin. Forms](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter16.md)
