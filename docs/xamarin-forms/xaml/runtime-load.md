---
title: Il caricamento di XAML in fase di esecuzione in xamarin. Forms
description: XAML può essere caricato e analizzato in fase di esecuzione con i metodi di estensione LoadFromXaml.
ms.prod: xamarin
ms.assetid: 25F73FBF-2DD3-468E-A2D8-0897414F0F4A
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/12/2018
ms.openlocfilehash: ce8ba32a1a6a1f69033615558c7ebf15d41e70fe
ms.sourcegitcommit: f890b5ec9b7c2702875070859e1a8cbf6e870e46
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/28/2018
ms.locfileid: "53814099"
---
# <a name="loading-xaml-at-runtime-in-xamarinforms"></a>Il caricamento di XAML in fase di esecuzione in xamarin. Forms

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://developer.xamarin.com/samples/xamarin-forms/XAML/LoadRuntimeXAML/)

Il [ `Xamarin.Forms.Xaml` ](xref:Xamarin.Forms.Xaml) dello spazio dei nomi include due [ `LoadFromXaml` ](xref:Xamarin.Forms.Xaml.Extensions.LoadFromXaml*) metodi di estensione che possono essere utilizzati per caricare e analizzare XAML in fase di esecuzione.

## <a name="background"></a>Sfondo

Quando viene creata una classe XAML di xamarin. Forms, il [ `LoadFromXaml` ](xref:Xamarin.Forms.Xaml.Extensions.LoadFromXaml*) metodo viene chiamato indirettamente. Ciò si verifica perché il file code-behind per un XAML classe chiama il `InitializeComponent` metodo dal relativo costruttore:

```csharp
public partial class MainPage : ContentPage
{
    public MainPage()
    {
        InitializeComponent();
    }
}
```

Quando Visual Studio viene compilato un progetto che contiene un file XAML, analizza il file XAML per generare un C# file di codice (ad esempio, **MainPage.xaml.g.cs**) che contiene la definizione del `InitializeComponent` metodo:

```csharp
private void InitializeComponent()
{
    global::Xamarin.Forms.Xaml.Extensions.LoadFromXaml(this, typeof(MainPage));
    ...
}
```

Il `InitializeComponent` chiamate al metodo il [ `LoadFromXaml` ](xref:Xamarin.Forms.Xaml.Extensions.LoadFromXaml*) metodo per estrarre il file XAML (o il file binario compilato) dalla libreria .NET Standard. Dopo l'estrazione, inizializza tutti gli oggetti definiti nel file XAML, li connette tutti gli elementi in relazioni padre-figlio, gestori di eventi definiti nel codice per gli eventi impostato nel file XAML e imposta l'albero risultante di oggetti come contenuto del pagina.

## <a name="loading-xaml-at-runtime"></a>Il caricamento di XAML in fase di esecuzione

Il [ `LoadFromXaml` ](xref:Xamarin.Forms.Xaml.Extensions.LoadFromXaml*) metodi sono `public`, pertanto può essere chiamato dalle applicazioni xamarin. Forms per caricare e analizzare XAML in fase di esecuzione. Questo consente scenari, ad esempio un'applicazione download XAML da un servizio web, creare la visualizzazione desiderata dal XAML e la relativa visualizzazione dell'applicazione.

> [!WARNING]
> Il caricamento di XAML in fase di esecuzione ha una significativa riduzione delle prestazioni e deve essere generalmente evitato.

Esempio di codice seguente illustra un uso semplice:

```csharp
using Xamarin.Forms.Xaml;
...

string navigationButtonXAML = "<Button Text=\"Navigate\" />";
Button navigationButton = new Button().LoadFromXaml(navigationButtonXAML);
...
_stackLayout.Children.Add(navigationButton);
```

In questo esempio, un [ `Button` ](xref:Xamarin.Forms.Button) istanza viene creata, con relativa [ `Text` ](xref:Xamarin.Forms.Button.Text) valore della proprietà viene impostato dal XAML definito nel `string`. Il `Button` viene quindi aggiunto a un [ `StackLayout` ](xref:Xamarin.Forms.StackLayout) che è stato definito nel XAML della pagina.

> [!NOTE]
> Il [ `LoadFromXaml` ](xref:Xamarin.Forms.Xaml.Extensions.LoadFromXaml*) metodi di estensione consentono un argomento di tipo generico a essere specificato. Tuttavia, è raramente la necessità di specificare l'argomento tipo, poiché verrà dedotto dal tipo dell'istanza relativo lavorando.

Il [ `LoadFromXaml` ](xref:Xamarin.Forms.Xaml.Extensions.LoadFromXaml*) metodo può essere utilizzato ingrandimento qualsiasi XAML, con il seguente esempio ciò fa aumentare erroneamente un [ `ContentPage` ](xref:Xamarin.Forms.ContentPage) e quindi spostandosi su di esso:

```csharp
using Xamarin.Forms.Xaml;
...

// See the sample for the full XAML string
string pageXAML = "<?xml version=\"1.0\" encoding=\"utf-8\"?>\r\n<ContentPage xmlns=\"http://xamarin.com/schemas/2014/forms\"\nxmlns:x=\"http://schemas.microsoft.com/winfx/2009/xaml\"\nx:Class=\"LoadRuntimeXAML.CatalogItemsPage\"\nTitle=\"Catalog Items\">\n</ContentPage>";

ContentPage page = new ContentPage().LoadFromXaml(pageXAML);
await Navigation.PushAsync(page);
```

## <a name="accessing-elements"></a>L'accesso agli elementi

Il caricamento di XAML in fase di esecuzione con il [ `LoadFromXaml` ](xref:Xamarin.Forms.Xaml.Extensions.LoadFromXaml*) metodo non consente l'accesso fortemente tipizzato per gli elementi XAML che sono specificati i nomi degli oggetti di runtime (tramite `x:Name`). Tuttavia, questi elementi XAML possono essere recuperati tramite il [ `FindByName` ](xref:Xamarin.Forms.NameScopeExtensions.FindByName*) (metodo) e quindi accedervi in base alle esigenze:

```csharp
// See the sample for the full XAML string
string pageXAML = "<?xml version=\"1.0\" encoding=\"utf-8\"?>\r\n<ContentPage xmlns=\"http://xamarin.com/schemas/2014/forms\"\nxmlns:x=\"http://schemas.microsoft.com/winfx/2009/xaml\"\nx:Class=\"LoadRuntimeXAML.CatalogItemsPage\"\nTitle=\"Catalog Items\">\n<StackLayout>\n<Label x:Name=\"monkeyName\"\n />\n</StackLayout>\n</ContentPage>";
ContentPage page = new ContentPage().LoadFromXaml(pageXAML);

Label monkeyLabel = page.FindByName<Label>("monkeyName");
monkeyLabel.Text = "Seated Monkey";
...
```

In questo esempio, il XAML per un [ `ContentPage` ](xref:Xamarin.Forms.ContentPage) viene ingrandito. Questo XAML include un [ `Label` ](xref:Xamarin.Forms.Label) denominato `monkeyName`, che viene recuperato utilizzando il [ `FindByName` ](xref:Xamarin.Forms.NameScopeExtensions.FindByName*) (metodo), prima che abbia [ `Text` ](xref:Xamarin.Forms.Label.Text) proprietà è impostata.

## <a name="related-links"></a>Collegamenti correlati

- [LoadRuntimeXAML (esempio)](https://developer.xamarin.com/samples/xamarin-forms/XAML/LoadRuntimeXAML/)
