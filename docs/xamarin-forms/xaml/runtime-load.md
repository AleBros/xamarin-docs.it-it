---
title: Caricamento di XAML in fase di esecuzione in Novell. Forms
description: XAML può essere caricato e analizzato in fase di esecuzione con i metodi di estensione LoadFromXaml.
ms.prod: xamarin
ms.assetid: 25F73FBF-2DD3-468E-A2D8-0897414F0F4A
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/12/2018
ms.openlocfilehash: e253d2ba949a94637d7773fdc50b479679fd3f41
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/30/2019
ms.locfileid: "68657247"
---
# <a name="loading-xaml-at-runtime-in-xamarinforms"></a>Caricamento di XAML in fase di esecuzione in Novell. Forms

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xaml-loadruntimexaml)

Lo [`Xamarin.Forms.Xaml`](xref:Xamarin.Forms.Xaml) spazio dei nomi [`LoadFromXaml`](xref:Xamarin.Forms.Xaml.Extensions.LoadFromXaml*) include due metodi di estensione che possono essere usati per caricare e analizzare XAML in fase di esecuzione.

## <a name="background"></a>Sfondo

Quando viene costruita una classe XAML Novell. Forms, [`LoadFromXaml`](xref:Xamarin.Forms.Xaml.Extensions.LoadFromXaml*) il metodo viene chiamato indirettamente. Questo problema si verifica perché il file code-behind per una classe XAML `InitializeComponent` chiama il metodo dal relativo costruttore:

```csharp
public partial class MainPage : ContentPage
{
    public MainPage()
    {
        InitializeComponent();
    }
}
```

Quando Visual Studio compila un progetto contenente un file XAML, analizza il file XAML per generare un C# file di codice (ad esempio, **MainPage.XAML.g.cs**) che contiene la `InitializeComponent` definizione del metodo:

```csharp
private void InitializeComponent()
{
    global::Xamarin.Forms.Xaml.Extensions.LoadFromXaml(this, typeof(MainPage));
    ...
}
```

Il `InitializeComponent` metodo chiama il [`LoadFromXaml`](xref:Xamarin.Forms.Xaml.Extensions.LoadFromXaml*) metodo per estrarre il file XAML (o il relativo binario compilato) dalla libreria .NET standard. Dopo l'estrazione, Inizializza tutti gli oggetti definiti nel file XAML, li connette tutti insieme nelle relazioni padre-figlio, associa i gestori eventi definiti nel codice agli eventi impostati nel file XAML e imposta l'albero risultante degli oggetti come contenuto del pagina.

## <a name="loading-xaml-at-runtime"></a>Caricamento di XAML in fase di esecuzione

I [`LoadFromXaml`](xref:Xamarin.Forms.Xaml.Extensions.LoadFromXaml*) metodi sono `public`, quindi possono essere chiamati dalle applicazioni Novell. Forms per caricare e analizzare XAML in fase di esecuzione. Questo consente scenari come un'applicazione che Scarica XAML da un servizio Web, creando la vista richiesta dal codice XAML e visualizzandola nell'applicazione.

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

In questo esempio viene creata [`Button`](xref:Xamarin.Forms.Button) un'istanza con [`Text`](xref:Xamarin.Forms.Button.Text) il valore della proprietà impostato dal codice XAML definito in `string`. Viene quindi aggiunto a un oggetto [`StackLayout`](xref:Xamarin.Forms.StackLayout) definito nel file XAML per la pagina. `Button`

> [!NOTE]
> I [`LoadFromXaml`](xref:Xamarin.Forms.Xaml.Extensions.LoadFromXaml*) metodi di estensione consentono di specificare un argomento di tipo generico. Tuttavia, raramente è necessario specificare l'argomento di tipo, poiché verrà dedotto dal tipo dell'istanza su cui opera.

Il [`LoadFromXaml`](xref:Xamarin.Forms.Xaml.Extensions.LoadFromXaml*) metodo può essere usato per ingrandire qualsiasi codice XAML, con l'esempio seguente per inflatare un oggetto [`ContentPage`](xref:Xamarin.Forms.ContentPage) e quindi passare a esso:

```csharp
using Xamarin.Forms.Xaml;
...

// See the sample for the full XAML string
string pageXAML = "<?xml version=\"1.0\" encoding=\"utf-8\"?>\r\n<ContentPage xmlns=\"http://xamarin.com/schemas/2014/forms\"\nxmlns:x=\"http://schemas.microsoft.com/winfx/2009/xaml\"\nx:Class=\"LoadRuntimeXAML.CatalogItemsPage\"\nTitle=\"Catalog Items\">\n</ContentPage>";

ContentPage page = new ContentPage().LoadFromXaml(pageXAML);
await Navigation.PushAsync(page);
```

## <a name="accessing-elements"></a>Accesso agli elementi

Il caricamento di XAML in fase [`LoadFromXaml`](xref:Xamarin.Forms.Xaml.Extensions.LoadFromXaml*) di esecuzione con il metodo non consente l'accesso fortemente tipizzato agli elementi XAML che hanno specificato nomi di `x:Name`oggetti di runtime (usando). Tuttavia, è possibile recuperare questi elementi XAML usando il [`FindByName`](xref:Xamarin.Forms.NameScopeExtensions.FindByName*) metodo e quindi accedervi come richiesto:

```csharp
// See the sample for the full XAML string
string pageXAML = "<?xml version=\"1.0\" encoding=\"utf-8\"?>\r\n<ContentPage xmlns=\"http://xamarin.com/schemas/2014/forms\"\nxmlns:x=\"http://schemas.microsoft.com/winfx/2009/xaml\"\nx:Class=\"LoadRuntimeXAML.CatalogItemsPage\"\nTitle=\"Catalog Items\">\n<StackLayout>\n<Label x:Name=\"monkeyName\"\n />\n</StackLayout>\n</ContentPage>";
ContentPage page = new ContentPage().LoadFromXaml(pageXAML);

Label monkeyLabel = page.FindByName<Label>("monkeyName");
monkeyLabel.Text = "Seated Monkey";
...
```

In questo esempio, il codice XAML per [`ContentPage`](xref:Xamarin.Forms.ContentPage) un oggetto è inflat. Questo codice XAML include [`Label`](xref:Xamarin.Forms.Label) un `monkeyName`oggetto denominato, che viene recuperato [`FindByName`](xref:Xamarin.Forms.NameScopeExtensions.FindByName*) utilizzando il metodo, prima [`Text`](xref:Xamarin.Forms.Label.Text) che venga impostata la proprietà.

## <a name="related-links"></a>Collegamenti correlati

- [LoadRuntimeXAML (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xaml-loadruntimexaml)
