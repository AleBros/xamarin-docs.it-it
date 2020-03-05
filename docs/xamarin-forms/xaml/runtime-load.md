---
title: Caricamento di XAML in fase di esecuzione in Novell. Forms
description: XAML può essere caricato e analizzato in fase di esecuzione con i metodi di estensione LoadFromXaml.
ms.prod: xamarin
ms.assetid: 25F73FBF-2DD3-468E-A2D8-0897414F0F4A
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/12/2018
ms.openlocfilehash: 71f510cd37d4bed2a5a6077ed63f748ce9894725
ms.sourcegitcommit: ae5557c5024d4b7bd52b2f33cb96114ce2b8e086
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/06/2020
ms.locfileid: "78292588"
---
# <a name="loading-xaml-at-runtime-in-xamarinforms"></a>Caricamento di XAML in fase di esecuzione in Novell. Forms

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xaml-loadruntimexaml)

Lo spazio dei nomi [`Xamarin.Forms.Xaml`](xref:Xamarin.Forms.Xaml) include due [`LoadFromXaml`](xref:Xamarin.Forms.Xaml.Extensions.LoadFromXaml*) metodi di estensione che possono essere usati per caricare e analizzare XAML in fase di esecuzione.

## <a name="background"></a>Background

Quando viene costruita una classe XAML Novell. Forms, viene chiamato indirettamente il metodo [`LoadFromXaml`](xref:Xamarin.Forms.Xaml.Extensions.LoadFromXaml*) . Questo problema si verifica perché il file code-behind per una classe XAML chiama il metodo `InitializeComponent` dal relativo costruttore:

```csharp
public partial class MainPage : ContentPage
{
    public MainPage()
    {
        InitializeComponent();
    }
}
```

Quando Visual Studio compila un progetto contenente un file XAML, analizza il file XAML per generare un C# file di codice (ad esempio, **MainPage.XAML.g.cs**) che contiene la definizione del metodo `InitializeComponent`:

```csharp
private void InitializeComponent()
{
    global::Xamarin.Forms.Xaml.Extensions.LoadFromXaml(this, typeof(MainPage));
    ...
}
```

Il metodo `InitializeComponent` chiama il metodo [`LoadFromXaml`](xref:Xamarin.Forms.Xaml.Extensions.LoadFromXaml*) per estrarre il file XAML (o il relativo file binario compilato) dalla libreria .NET standard. Dopo l'estrazione, Inizializza tutti gli oggetti definiti nel file XAML, li connette tutti insieme nelle relazioni padre-figlio, associa i gestori eventi definiti nel codice agli eventi impostati nel file XAML e imposta l'albero risultante degli oggetti come contenuto del pagina.

## <a name="loading-xaml-at-runtime"></a>Caricamento di XAML in fase di esecuzione

I [`LoadFromXaml`](xref:Xamarin.Forms.Xaml.Extensions.LoadFromXaml*) metodi sono `public`e pertanto possono essere chiamati dalle applicazioni Novell. Forms per caricare e analizzare XAML in fase di esecuzione. Questo consente scenari come un'applicazione che Scarica XAML da un servizio Web, creando la vista richiesta dal codice XAML e visualizzandola nell'applicazione.

> [!WARNING]
> Il caricamento del codice XAML in fase di esecuzione comporta un notevole costo in termini di prestazioni e deve essere evitato.

Nell'esempio di codice riportato di seguito viene illustrato un utilizzo semplice:

```csharp
using Xamarin.Forms.Xaml;
...

string navigationButtonXAML = "<Button Text=\"Navigate\" />";
Button navigationButton = new Button().LoadFromXaml(navigationButtonXAML);
...
_stackLayout.Children.Add(navigationButton);
```

In questo esempio viene creata un'istanza di [`Button`](xref:Xamarin.Forms.Button) con il relativo valore di proprietà [`Text`](xref:Xamarin.Forms.Button.Text) impostato dal codice XAML definito nella `string`. Il `Button` viene quindi aggiunto a un [`StackLayout`](xref:Xamarin.Forms.StackLayout) definito nel file XAML per la pagina.

> [!NOTE]
> I metodi di estensione [`LoadFromXaml`](xref:Xamarin.Forms.Xaml.Extensions.LoadFromXaml*) consentono di specificare un argomento di tipo generico. Tuttavia, raramente è necessario specificare l'argomento di tipo, poiché verrà dedotto dal tipo dell'istanza su cui opera.

Il metodo [`LoadFromXaml`](xref:Xamarin.Forms.Xaml.Extensions.LoadFromXaml*) può essere usato per ingrandire qualsiasi codice XAML, con l'esempio seguente che ingrandisce una [`ContentPage`](xref:Xamarin.Forms.ContentPage) e quindi vi si sposta:

```csharp
using Xamarin.Forms.Xaml;
...

// See the sample for the full XAML string
string pageXAML = "<?xml version=\"1.0\" encoding=\"utf-8\"?>\r\n<ContentPage xmlns=\"http://xamarin.com/schemas/2014/forms\"\nxmlns:x=\"http://schemas.microsoft.com/winfx/2009/xaml\"\nx:Class=\"LoadRuntimeXAML.CatalogItemsPage\"\nTitle=\"Catalog Items\">\n</ContentPage>";

ContentPage page = new ContentPage().LoadFromXaml(pageXAML);
await Navigation.PushAsync(page);
```

## <a name="accessing-elements"></a>Accesso agli elementi

Il caricamento di XAML in fase di esecuzione con il metodo [`LoadFromXaml`](xref:Xamarin.Forms.Xaml.Extensions.LoadFromXaml*) non consente l'accesso fortemente tipizzato agli elementi XAML che hanno specificato nomi di oggetti di runtime (usando `x:Name`). Tuttavia, è possibile recuperare questi elementi XAML usando il metodo [`FindByName`](xref:Xamarin.Forms.NameScopeExtensions.FindByName*) e quindi accedervi come richiesto:

```csharp
// See the sample for the full XAML string
string pageXAML = "<?xml version=\"1.0\" encoding=\"utf-8\"?>\r\n<ContentPage xmlns=\"http://xamarin.com/schemas/2014/forms\"\nxmlns:x=\"http://schemas.microsoft.com/winfx/2009/xaml\"\nx:Class=\"LoadRuntimeXAML.CatalogItemsPage\"\nTitle=\"Catalog Items\">\n<StackLayout>\n<Label x:Name=\"monkeyName\"\n />\n</StackLayout>\n</ContentPage>";
ContentPage page = new ContentPage().LoadFromXaml(pageXAML);

Label monkeyLabel = page.FindByName<Label>("monkeyName");
monkeyLabel.Text = "Seated Monkey";
...
```

In questo esempio, il codice XAML per un [`ContentPage`](xref:Xamarin.Forms.ContentPage) è inflat. Questo codice XAML include un [`Label`](xref:Xamarin.Forms.Label) denominato `monkeyName`, che viene recuperato utilizzando il metodo [`FindByName`](xref:Xamarin.Forms.NameScopeExtensions.FindByName*) , prima che venga impostata la relativa proprietà [`Text`](xref:Xamarin.Forms.Label.Text) .

## <a name="related-links"></a>Collegamenti correlati

- [LoadRuntimeXAML (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xaml-loadruntimexaml)
