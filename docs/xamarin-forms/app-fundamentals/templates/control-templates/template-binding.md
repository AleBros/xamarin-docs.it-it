---
title: L'associazione da un oggetto ControlTemplate xamarin. Forms
description: Associazioni a modello consentono di associare i controlli in un modello di controllo per i dati a proprietà pubbliche, l'abilitazione di valori delle proprietà dei controlli nel modello del controllo da modificare con facilità. Questo articolo illustra l'uso di associazioni a modello per eseguire il data binding da un modello di controllo.
ms.prod: xamarin
ms.assetid: 794A663C-3A8D-438A-BD02-8E97C919B55F
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/08/2016
ms.openlocfilehash: 13730dce5d4698085abe10cb93da5ba50b87ab01
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2018
ms.locfileid: "50106429"
---
# <a name="binding-from-a-xamarinforms-controltemplate"></a>L'associazione da un oggetto ControlTemplate xamarin. Forms

_Associazioni a modello consentono di associare i controlli in un modello di controllo per i dati a proprietà pubbliche, l'abilitazione di valori delle proprietà dei controlli nel modello del controllo da modificare con facilità. Questo articolo illustra l'uso di associazioni a modello per eseguire il data binding da un modello di controllo._

Oggetto [ `TemplateBinding` ](xref:Xamarin.Forms.TemplateBinding) viene utilizzato per associare una proprietà del controllo in un modello di controllo a una proprietà associabile dell'elemento padre del *destinazione* visualizzazione a cui appartiene il modello di controllo. Ad esempio, invece di definire il testo visualizzato da [ `Label` ](xref:Xamarin.Forms.Label) istanze all'interno di [ `ControlTemplate` ](xref:Xamarin.Forms.ControlTemplate), è possibile usare un'associazione di modelli per eseguire l'associazione il [ `Label.Text` ](xref:Xamarin.Forms.Label.Text) proprietà per proprietà associabili che definiscono il testo da visualizzare.

Oggetto [ `TemplateBinding` ](xref:Xamarin.Forms.TemplateBinding) è simile a un oggetto esistente [ `Binding` ](xref:Xamarin.Forms.Binding), ad eccezione del fatto che il *origine* di un `TemplateBinding` è impostato sempre automaticamente al padre del *destinazione* visualizzazione a cui appartiene il modello di controllo. Si noti tuttavia che l'utilizzo un `TemplateBinding` all'esterno di un [ `ControlTemplate` ](xref:Xamarin.Forms.ControlTemplate) non è supportato.

## <a name="creating-a-templatebinding-in-xaml"></a>Creazione di un TemplateBinding in XAML

In XAML, un [ `TemplateBinding` ](xref:Xamarin.Forms.TemplateBinding) viene creato utilizzando la [ `TemplateBinding` ](xref:Xamarin.Forms.Xaml.TemplateBindingExtension) estensione di markup, come illustrato nell'esempio di codice seguente:

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

Anziché impostare il [ `Label.Text` ](xref:Xamarin.Forms.Label.Text) le proprietà di testo statico, le proprietà possono usare le associazioni a modello da associare alla proprietà associabili dell'elemento padre del *destinazione* visualizzazione a cui appartiene il [ `ControlTemplate`](xref:Xamarin.Forms.ControlTemplate). Si noti tuttavia che le associazioni del modello associato al `Parent.HeaderText` e `Parent.FooterText`, anziché `HeaderText` e `FooterText`. Infatti, in questo esempio, le proprietà associabili sono definite nel nodo padre del padre di *destinazione* visualizzare, anziché l'elemento padre, come illustrato nell'esempio di codice seguente:

```xaml
<ContentPage ...>
    <ContentView ... ControlTemplate="{StaticResource TealTemplate}">
          ...
    </ContentView>
</ContentPage>
```

Il *origine* il modello di associazione è sempre impostata automaticamente al padre delle *destinazione* visualizzazione a cui appartiene il modello di controllo, di seguito è riportato il [ `ContentView` ](xref:Xamarin.Forms.ContentView) istanza. Il modello di associazione Usa la [ `Parent` ](xref:Xamarin.Forms.Element.Parent) proprietà per restituire l'elemento padre del `ContentView` istanza, che è la [ `ContentPage` ](xref:Xamarin.Forms.ContentPage) istanza. Di conseguenza, usando un [ `TemplateBinding` ](xref:Xamarin.Forms.TemplateBinding) nel [ `ControlTemplate` ](xref:Xamarin.Forms.ControlTemplate) da associare alla `Parent.HeaderText` e `Parent.FooterText` individua le proprietà associabili che sono definite nel `ContentPage`, come illustrato nell'esempio di codice seguente:

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

Ciò comporta l'aspetto illustrato negli screenshot seguenti:

![](template-binding-images/teal-theme.png "Modello di controllo verde acqua con associazioni a modello")

## <a name="creating-a-templatebinding-in-c35"></a>Creazione di un TemplateBinding in C&#35;

In c#, un [ `TemplateBinding` ](xref:Xamarin.Forms.TemplateBinding) viene creato usando il `TemplateBinding` costruttore, come illustrato nell'esempio di codice seguente:

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

Anziché impostare il [ `Label.Text` ](xref:Xamarin.Forms.Label.Text) le proprietà di testo statico, le proprietà possono usare le associazioni a modello da associare alla proprietà associabili dell'elemento padre del *destinazione* visualizzazione a cui appartiene il [ `ControlTemplate`](xref:Xamarin.Forms.ControlTemplate). L'associazione di modelli viene creato usando il [ `SetBinding` ](xref:Xamarin.Forms.BindableObject.SetBinding(Xamarin.Forms.BindableProperty,Xamarin.Forms.BindingBase)) metodo, specificando un [ `TemplateBinding` ](xref:Xamarin.Forms.TemplateBinding) istanza come secondo parametro. Si noti che le associazioni del modello associati a `Parent.HeaderText` e `Parent.FooterText`, perché le proprietà associabili sono definite nel nodo padre del padre le *destinazione* visualizzare, anziché l'elemento padre, come illustrato nell'esempio di codice seguente:

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

Le proprietà associabili sono definite nel `ContentPage`, come indicato in precedenza.

### <a name="binding-a-bindableproperty-to-a-viewmodel-property"></a>Associazione di un BindableProperty a una proprietà ViewModel

Come già indicato, una [ `TemplateBinding` ](xref:Xamarin.Forms.TemplateBinding) associa una proprietà del controllo in un modello di controllo a una proprietà associabile dell'elemento padre del *destinazione* visualizzazione a cui appartiene il modello di controllo. Queste proprietà associabili a sua volta, può essere associata alle proprietà nel ViewModel.

Esempio di codice seguente definisce due proprietà in un elemento ViewModel:

```csharp
public class HomePageViewModel
{
  public string HeaderText { get { return "Control Template Demo App"; } }
  public string FooterText { get { return "(c) Xamarin 2016"; } }
}
```

Il `HeaderText` e `FooterText` proprietà ViewModel può essere associata, come illustrato nell'esempio di codice XAML seguente:

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

Il `HeaderText` e `FooterText` proprietà associabili sono associate ai `HomePageViewModel.HeaderText` e `HomePageViewModel.FooterText` le proprietà, a causa dell'impostazione il [ `BindingContext` ](xref:Xamarin.Forms.BindableObject.BindingContext) a un'istanza del `HomePageViewModel` classe. In generale, di conseguenza le proprietà di controllo nel [ `ControlTemplate` ](xref:Xamarin.Forms.ControlTemplate) a cui è associata [ `BindableProperty` ](xref:Xamarin.Forms.BindableProperty) istanze il [ `ContentPage` ](xref:Xamarin.Forms.ContentPage), che a sua volta associare a Proprietà ViewModel.

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

È anche possibile associare alle proprietà del modello di visualizzazione direttamente, in modo che non è necessario dichiarare `BindableProperty`istanze `HeaderText` e `FooterText` sul `ContentPage`, associando il modello di controllo a Parent.BindingContext. _PropertyName_ , ad esempio:

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

Per altre informazioni sul data binding al ViewModel, vedere [dal Data Binding a MVVM](~/xamarin-forms/xaml/xaml-basics/data-bindings-to-mvvm.md).

## <a name="summary"></a>Riepilogo

Questo articolo illustrato l'uso di associazioni di modello per eseguire l'associazione di dati da un modello di controllo. Associazioni a modello consentono di associare i controlli in un modello di controllo per i dati a proprietà pubbliche, l'abilitazione di valori delle proprietà dei controlli nel modello del controllo da modificare con facilità.

## <a name="related-links"></a>Collegamenti correlati

- [Nozioni fondamentali sull'associazione dati](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md)
- [Da Data Binding a MVVM](~/xamarin-forms/xaml/xaml-basics/data-bindings-to-mvvm.md)
- [Tema semplice con modello di associazione (esempio)](https://developer.xamarin.com/samples/xamarin-forms/templates/controltemplates/simplethemewithtemplatebinding/)
- [Tema semplice con l'associazione di modelli e ViewModel (esempio)](https://developer.xamarin.com/samples/xamarin-forms/templates/controltemplates/simplethemewithtemplatebindingandviewmodel/)
- [TemplateBinding](xref:Xamarin.Forms.TemplateBinding)
- [Oggetto ControlTemplate](xref:Xamarin.Forms.ControlTemplate)
- [ContentView](xref:Xamarin.Forms.ContentView)
