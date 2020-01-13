---
title: Associazione da un ControlTemplate Xamarin.Forms
description: Le associazioni a modello consentono di associare dati dei controlli di un modello del controllo a proprietà pubbliche, permettendo la modifica intuitiva dei valori delle proprietà dei controlli nel modello del controllo. Questo articolo illustra come usare le associazioni a modello per eseguire data binding da un modello del controllo.
ms.prod: xamarin
ms.assetid: 794A663C-3A8D-438A-BD02-8E97C919B55F
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/08/2016
ms.openlocfilehash: 41e5bbc42ccde5cdd5223a7d2cb0a77da66e10c1
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/30/2019
ms.locfileid: "68647010"
---
# <a name="binding-from-a-xamarinforms-controltemplate"></a>Associazione da un ControlTemplate Xamarin.Forms

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/templates-controltemplates-simplethemewithtemplatebinding)

_Le associazioni a modello consentono di associare dati dei controlli di un modello del controllo a proprietà pubbliche, permettendo la modifica intuitiva dei valori delle proprietà dei controlli nel modello del controllo. Questo articolo illustra come usare le associazioni a modello per eseguire data binding da un modello del controllo._

[`TemplateBinding`](xref:Xamarin.Forms.TemplateBinding) viene usato per associare una proprietà di un controllo in un modello di controllo a una proprietà associabile nel padre della visualizzazione di *destinazione* a cui appartiene il modello del controllo. Ad esempio, invece di definire il testo visualizzato dalle istanze di [`Label`](xref:Xamarin.Forms.Label) all'interno di [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate), è possibile usare un'associazione a modello per associare la proprietà [`Label.Text`](xref:Xamarin.Forms.Label.Text) alle proprietà associabili che definiscono il testo da visualizzare.

[`TemplateBinding`](xref:Xamarin.Forms.TemplateBinding) è simile a un [`Binding`](xref:Xamarin.Forms.Binding) esistente, ad eccezione del fatto che l'*origine* di un `TemplateBinding` viene sempre impostata automaticamente sul padre della visualizzazione di *destinazione* a cui appartiene il modello del controllo. Si noti, tuttavia, che l'uso di un `TemplateBinding` all'esterno di un [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) non è supportato.

## <a name="creating-a-templatebinding-in-xaml"></a>Creazione di un TemplateBinding in XAML

In XAML, un [`TemplateBinding`](xref:Xamarin.Forms.TemplateBinding) viene creato usando l'estensione di markup [`TemplateBinding`](xref:Xamarin.Forms.Xaml.TemplateBindingExtension), come illustrato nell'esempio di codice seguente:

```xaml
<ControlTemplate x:Key="TealTemplate">
  <Grid>
    ...
    <Label Text="{TemplateBinding Parent.HeaderText}" ... />
    ...
    <Label Text="{TemplateBinding Parent.FooterText}" ... />
  </Grid>
</ControlTemplate>
```

Anziché impostare le proprietà [`Label.Text`](xref:Xamarin.Forms.Label.Text) su testo statico, le proprietà possono usare le associazioni a modello per l'associazione a proprietà associabili nel padre della visualizzazione di *destinazione* a cui appartiene [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate). Si noti, tuttavia, che le associazioni a modello si associano a `Parent.HeaderText` e `Parent.FooterText`, anziché a `HeaderText` e `FooterText`. Il motivo è che, in questo esempio, le proprietà associabili sono definite nel padre del padre della visualizzazione di *destinazione*, anziché nel padre, come illustrato nell'esempio di codice seguente:

```xaml
<ContentPage ...>
    <ContentView ... ControlTemplate="{StaticResource TealTemplate}">
          ...
    </ContentView>
</ContentPage>
```

L'*origine* dell'associazione a modello viene sempre impostata automaticamente sul padre della visualizzazione di *destinazione* a cui appartiene il modello di controllo, che in questo caso è l'istanza [`ContentView`](xref:Xamarin.Forms.ContentView). L'associazione a modello usa la proprietà [`Parent`](xref:Xamarin.Forms.Element.Parent) per restituire l'elemento padre dell'istanza di `ContentView`, che è l'istanza di [`ContentPage`](xref:Xamarin.Forms.ContentPage). Di conseguenza, usando un [`TemplateBinding`](xref:Xamarin.Forms.TemplateBinding) in [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) per l'associazione a `Parent.HeaderText` e a `Parent.FooterText` vengono individuate le proprietà associabili definite in `ContentPage`, come illustrato nell'esempio di codice seguente:

```csharp
public static readonly BindableProperty HeaderTextProperty =
  BindableProperty.Create ("HeaderText", typeof(string), typeof(HomePage), "Control Template Demo App");
public static readonly BindableProperty FooterTextProperty =
  BindableProperty.Create ("FooterText", typeof(string), typeof(HomePage), "(c) Xamarin 2016");

public string HeaderText {
  get { return (string)GetValue (HeaderTextProperty); }
}

public string FooterText {
  get { return (string)GetValue (FooterTextProperty); }
}
```

Il risultato è l'aspetto illustrato negli screenshot seguenti:

![](template-binding-images/teal-theme.png "Modello del controllo verde acqua con associazioni a modello")

## <a name="creating-a-templatebinding-in-c35"></a>Creazione di un TemplateBinding in C&#35;

In C#, un [`TemplateBinding`](xref:Xamarin.Forms.TemplateBinding) viene creato tramite il costruttore `TemplateBinding`, come illustrato nell'esempio di codice seguente:

```csharp
class TealTemplate : Grid
{
  public TealTemplate ()
  {
    ...
    var topLabel = new Label { TextColor = Color.White, VerticalOptions = LayoutOptions.Center };
    topLabel.SetBinding (Label.TextProperty, new TemplateBinding ("Parent.HeaderText"));
    ...
    var bottomLabel = new Label { TextColor = Color.White, VerticalOptions = LayoutOptions.Center };
    bottomLabel.SetBinding (Label.TextProperty, new TemplateBinding ("Parent.FooterText"));
    ...
  }
}
```

Anziché impostare le proprietà [`Label.Text`](xref:Xamarin.Forms.Label.Text) su testo statico, le proprietà possono usare le associazioni a modello per l'associazione a proprietà associabili nel padre della visualizzazione di *destinazione* a cui appartiene [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate). L'associazione a modello viene creata tramite il metodo [`SetBinding`](xref:Xamarin.Forms.BindableObject.SetBinding(Xamarin.Forms.BindableProperty,Xamarin.Forms.BindingBase)), specificando un'istanza di [`TemplateBinding`](xref:Xamarin.Forms.TemplateBinding) come secondo parametro. Si noti che le associazioni a modello impostano l'associazione a `Parent.HeaderText` e a `Parent.FooterText`, perché le proprietà associabili sono definite nel padre del padre della visualizzazione di *destinazione*, anziché nel padre, come illustrato nell'esempio di codice seguente:

```csharp
public class HomePageCS : ContentPage
{
  ...
  public HomePageCS ()
  {
    Content = new ContentView {
      ControlTemplate = tealTemplate,
      Content = new StackLayout {
        ...
      },
      ...
    };
    ...
  }
}
```

Le proprietà associabili sono definite in `ContentPage`, come indicato in precedenza.

### <a name="binding-a-bindableproperty-to-a-viewmodel-property"></a>Associazione di una BindableProperty a una proprietà ViewModel

Come già indicato, un [`TemplateBinding`](xref:Xamarin.Forms.TemplateBinding) consente di associare una proprietà di un controllo in un modello del controllo a una proprietà associabile nel padre della visualizzazione di *destinazione* a cui appartiene il modello di controllo. A sua volta, queste proprietà associabili possono essere associate alle proprietà in ViewModel.

L'esempio di codice seguente definisce due proprietà per un ViewModel:

```csharp
public class HomePageViewModel
{
  public string HeaderText { get { return "Control Template Demo App"; } }
  public string FooterText { get { return "(c) Xamarin 2016"; } }
}
```

Le proprietà `HeaderText` e `FooterText` di ViewModel possono essere la destinazione dell'associazione, come illustrato nell'esempio di codice XAML seguente:

```xaml
<ContentPage xmlns:local="clr-namespace:SimpleTheme;assembly=SimpleTheme"
             HeaderText="{Binding HeaderText}" FooterText="{Binding FooterText}" ...>
    <ContentPage.BindingContext>
        <local:HomePageViewModel />
    </ContentPage.BindingContext>
    <ContentView ControlTemplate="{StaticResource TealTemplate}" ...>
    ...
    </ContentView>
</ContentPage>
```

Le proprietà associabili `HeaderText` e `FooterText` vengono associate alle proprietà `HomePageViewModel.HeaderText` e `HomePageViewModel.FooterText`, a causa dell'impostazione di [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) su un'istanza della classe `HomePageViewModel`. In generale, il risultato è che le proprietà del controllo nel [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) vengono associate alle istanze di [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) in [`ContentPage`](xref:Xamarin.Forms.ContentPage), a loro volta associate a proprietà di ViewModel.

Il codice C# equivalente è visualizzato nell'esempio seguente:

```csharp
public class HomePageCS : ContentPage
{
  ...
  public HomePageCS ()
  {
    BindingContext = new HomePageViewModel ();
    this.SetBinding (HeaderTextProperty, "HeaderText");
    this.SetBinding (FooterTextProperty, "FooterText");
    ...
  }
}
```

È anche possibile impostare un'associazione diretta alle proprietà del modello di visualizzazione, in modo da non dover dichiarare `BindableProperty`per `HeaderText` e `FooterText` in `ContentPage`, associando il modello del controllo a Parent.BindingContext._NomeProprietà_, ad esempio:

```xaml
<ControlTemplate x:Key="TealTemplate">
  <Grid>
    ...
    <Label Text="{TemplateBinding Parent.BindingContext.HeaderText}" ... />
    ...
    <Label Text="{TemplateBinding Parent.BindingContext.FooterText}" ... />
  </Grid>
</ControlTemplate>
```

Per altre informazioni sul data binding a ViewModel, vedere [Da data binding a MVVM](~/xamarin-forms/xaml/xaml-basics/data-bindings-to-mvvm.md).

## <a name="summary"></a>Riepilogo

In questo articolo è stato illustrato come usare le associazioni a modello per eseguire data binding da un modello del controllo. Le associazioni a modello consentono di associare dati dei controlli di un modello del controllo a proprietà pubbliche, permettendo la modifica intuitiva dei valori delle proprietà dei controlli nel modello del controllo.

## <a name="related-links"></a>Collegamenti correlati

- [Nozioni di base sul data binding](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md)
- [Da data binding a MVVM](~/xamarin-forms/xaml/xaml-basics/data-bindings-to-mvvm.md)
- [Simple Theme with Template Binding (sample)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/templates-controltemplates-simplethemewithtemplatebinding) (Esempio di tema semplice con associazione a modello)
- [Simple Theme with Template Binding and ViewModel (sample)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/templates-controltemplates-simplethemewithtemplatebindingandviewmodel) (Esempio di tema semplice con associazione a modello e ViewModel)
- [TemplateBinding](xref:Xamarin.Forms.TemplateBinding)
- [ControlTemplate](xref:Xamarin.Forms.ControlTemplate)
- [ContentView](xref:Xamarin.Forms.ContentView)
