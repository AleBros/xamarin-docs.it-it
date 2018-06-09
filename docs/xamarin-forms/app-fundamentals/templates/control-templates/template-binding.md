---
title: Associazione di ControlTemplate xamarin. Forms
description: Associazioni a modello consentono di associare i controlli in un modello di controllo ai dati a proprietà pubbliche, abilitare i valori delle proprietà dei controlli nel modello di controllo per modificare con facilità. In questo articolo viene illustrato l'utilizzo di associazioni a modello per eseguire l'associazione di dati da un modello di controllo.
ms.prod: xamarin
ms.assetid: 794A663C-3A8D-438A-BD02-8E97C919B55F
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/08/2016
ms.openlocfilehash: 99d798ce2c74da0cf7fa0d497128db628a12ead5
ms.sourcegitcommit: 66682dd8e93c0e4f5dee69f32b5fc5a96443e307
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/08/2018
ms.locfileid: "35241581"
---
# <a name="binding-from-a-xamarinforms-controltemplate"></a>Associazione di ControlTemplate xamarin. Forms

_Associazioni a modello consentono di associare i controlli in un modello di controllo ai dati a proprietà pubbliche, abilitare i valori delle proprietà dei controlli nel modello di controllo per modificare con facilità. In questo articolo viene illustrato l'utilizzo di associazioni a modello per eseguire l'associazione di dati da un modello di controllo._

A [ `TemplateBinding` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TemplateBinding/) viene utilizzato per associare una proprietà associabile nell'elemento padre della proprietà di un controllo in un modello di controllo di *destinazione* visualizzazione cui appartiene il modello di controllo. Ad esempio, anziché definire il testo visualizzato dal [ `Label` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/) istanze all'interno di [ `ControlTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ControlTemplate/), è possibile utilizzare un'associazione di modelli per associare il [ `Label.Text` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Label.Text/) proprietà per proprietà associabile che definiscono il testo da visualizzare.

A [ `TemplateBinding` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TemplateBinding/) è simile a un oggetto esistente [ `Binding` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Binding/), ad eccezione del fatto che il *origine* di un `TemplateBinding` viene impostata automaticamente sempre l'elemento padre del *destinazione* visualizzazione cui appartiene il modello di controllo. Si noti tuttavia che l'utilizzo un `TemplateBinding` all'esterno di un [ `ControlTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ControlTemplate/) non è supportata.

## <a name="creating-a-templatebinding-in-xaml"></a>Creazione di un TemplateBinding in XAML

In XAML, un [ `TemplateBinding` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TemplateBinding/) viene creato utilizzando il [ `TemplateBinding` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Xaml.TemplateBindingExtension/) estensione di markup, come illustrato nell'esempio di codice seguente:

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

Anziché impostare la [ `Label.Text` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Label.Text/) proprietà in testo statico, le proprietà possono utilizzare associazioni a modello per associare le proprietà associabili dell'elemento padre del *destinazione* visualizzazione cui appartiene il [ `ControlTemplate`](https://developer.xamarin.com/api/type/Xamarin.Forms.ControlTemplate/). Si noti tuttavia che le associazioni di modello consente di associare `Parent.HeaderText` e `Parent.FooterText`, anziché `HeaderText` e `FooterText`. Infatti, in questo esempio, le proprietà associabili vengono definite nell'elemento padre del padre di *destinazione* visualizzare, anziché l'elemento padre, come illustrato nell'esempio di codice seguente:

```xaml
<ContentPage ...>
    <ContentView ... ControlTemplate="{StaticResource TealTemplate}">
          ...
    </ContentView>
</ContentPage>
```

Il *origine* del modello di associazione è impostata sempre automaticamente all'elemento padre il *destinazione* visualizzazione cui appartiene il modello di controllo, ecco la [ `ContentView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentView/) istanza. Il modello di associazione viene utilizzato il [ `Parent` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Element.Parent/) proprietà per restituire l'elemento padre del `ContentView` istanza, ovvero il [ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/) istanza. Pertanto, si utilizza un [ `TemplateBinding` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TemplateBinding/) nel [ `ControlTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ControlTemplate/) da associare alla `Parent.HeaderText` e `Parent.FooterText` individua le proprietà associabili definiti nel `ContentPage`, come illustrato nell'esempio di codice seguente:

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

Ciò comporta l'aspetto illustrato nelle schermate seguenti:

![](template-binding-images/teal-theme.png "Modello di controllo verde acqua con associazioni a modello")

## <a name="creating-a-templatebinding-in-c35"></a>Creazione di un TemplateBinding in C&#35;

In c#, un [ `TemplateBinding` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TemplateBinding/) viene creato utilizzando il `TemplateBinding` costruttore, come illustrato nell'esempio di codice seguente:

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

Anziché impostare la [ `Label.Text` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Label.Text/) proprietà in testo statico, le proprietà possono utilizzare associazioni a modello per associare le proprietà associabili dell'elemento padre del *destinazione* visualizzazione cui appartiene il [ `ControlTemplate`](https://developer.xamarin.com/api/type/Xamarin.Forms.ControlTemplate/). L'associazione del modello viene creata utilizzando il [ `SetBinding` ](https://developer.xamarin.com/api/member/Xamarin.Forms.BindableObject.SetBinding/p/Xamarin.Forms.BindableProperty/Xamarin.Forms.BindingBase/) metodo, specificando un [ `TemplateBinding` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TemplateBinding/) istanza come secondo parametro. Si noti che le associazioni di modello associare `Parent.HeaderText` e `Parent.FooterText`, poiché le proprietà associabili sono definite nell'elemento padre del padre di *destinazione* visualizzare, anziché l'elemento padre, come illustrato nell'esempio di codice seguente:

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

Le proprietà associabili vengono definite nel `ContentPage`, come indicato in precedenza.

### <a name="binding-a-bindableproperty-to-a-viewmodel-property"></a>Associazione di un BindableProperty a una proprietà ViewModel

Come già indicato, una [ `TemplateBinding` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TemplateBinding/) associa una proprietà del controllo in un modello di controllo a una proprietà associabile nell'elemento padre del *destinazione* visualizzazione cui appartiene il modello di controllo. Queste proprietà associabile a sua volta, possono essere associate a proprietà ViewModel.

Esempio di codice seguente definisce due proprietà in un elemento ViewModel:

```csharp
public class HomePageViewModel
{
  public string HeaderText { get { return "Control Template Demo App"; } }
  public string FooterText { get { return "(c) Xamarin 2016"; } }
}
```

Il `HeaderText` e `FooterText` ViewModel proprietà può essere associata, come illustrato nell'esempio di codice XAML seguente:

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

Il `HeaderText` e `FooterText` proprietà associabili sono associate il `HomePageViewModel.HeaderText` e `HomePageViewModel.FooterText` proprietà, a causa dell'impostazione di [ `BindingContext` ](https://developer.xamarin.com/api/property/Xamarin.Forms.BindableObject.BindingContext/) a un'istanza del `HomePageViewModel` classe. In generale, di conseguenza, le proprietà del controllo nel [ `ControlTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ControlTemplate/) da associare ai [ `BindableProperty` ](https://developer.xamarin.com/api/type/Xamarin.Forms.BindableProperty/) istanze di [ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/), che a sua volta associare Proprietà ViewModel.

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

Per ulteriori informazioni sull'associazione dati a ViewModel, vedere [da associazioni dati per MVVM](~/xamarin-forms/xaml/xaml-basics/data-bindings-to-mvvm.md).

## <a name="summary"></a>Riepilogo

In questo articolo è illustrato l'uso di associazioni di modello per eseguire l'associazione dati da un modello di controllo. Associazioni a modello consentono di associare i controlli in un modello di controllo ai dati a proprietà pubbliche, abilitare i valori delle proprietà dei controlli nel modello di controllo per modificare con facilità.



## <a name="related-links"></a>Collegamenti correlati

- [Nozioni fondamentali sull'associazione dati](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md)
- [Da associazioni di dati per MVVM](~/xamarin-forms/xaml/xaml-basics/data-bindings-to-mvvm.md)
- [Tema semplice con modello di associazione (esempio)](https://developer.xamarin.com/samples/xamarin-forms/templates/controltemplates/simplethemewithtemplatebinding/)
- [Tema semplice con modello di associazione e ViewModel (esempio)](https://developer.xamarin.com/samples/xamarin-forms/templates/controltemplates/simplethemewithtemplatebindingandviewmodel/)
- [TemplateBinding](https://developer.xamarin.com/api/type/Xamarin.Forms.TemplateBinding/)
- [ControlTemplate](https://developer.xamarin.com/api/type/Xamarin.Forms.ControlTemplate/)
- [ContentView](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentView/)
