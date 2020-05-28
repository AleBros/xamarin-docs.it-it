---
title: Caricamento di XAML in fase di esecuzione inXamarin.Forms
description: ''
ms.prod: ''
ms.assetid: ''
ms.technology: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: d750aa84a48ad4c8015a619d819134cefc63c3d9
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/28/2020
ms.locfileid: "84139373"
---
# <a name="loading-xaml-at-runtime-in-xamarinforms"></a>Caricamento di XAML in fase di esecuzione inXamarin.Forms

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xaml-loadruntimexaml)

Lo [`Xamarin.Forms.Xaml`](xref:Xamarin.Forms.Xaml) spazio dei nomi include due [`LoadFromXaml`](xref:Xamarin.Forms.Xaml.Extensions.LoadFromXaml*) metodi di estensione che possono essere usati per caricare e analizzare XAML in fase di esecuzione.

## <a name="background"></a>Background

Quando Xamarin.Forms viene costruita una classe XAML, [`LoadFromXaml`](xref:Xamarin.Forms.Xaml.Extensions.LoadFromXaml*) viene chiamato indirettamente il metodo. Questo problema si verifica perché il file code-behind per una classe XAML chiama il `InitializeComponent` metodo dal relativo costruttore:

```csharp
public partial class MainPage : ContentPage
{
    public MainPage()
    {
        InitializeComponent();
    }
}
```

Quando Visual Studio compila un progetto contenente un file XAML, analizza il file XAML per generare un file di codice C# (ad esempio, **MainPage.XAML.g.cs**) che contiene la definizione del `InitializeComponent` Metodo:

```csharp
private void InitializeComponent()
{
    global::Xamarin.Forms.Xaml.Extensions.LoadFromXaml(this, typeof(MainPage));
    ...
}
```

Il `InitializeComponent` metodo chiama il [`LoadFromXaml`](xref:Xamarin.Forms.Xaml.Extensions.LoadFromXaml*) metodo per estrarre il file XAML (o il relativo binario compilato) dalla libreria .NET standard. Dopo l'estrazione, Inizializza tutti gli oggetti definiti nel file XAML, li connette tutti insieme nelle relazioni padre-figlio, associa i gestori eventi definiti nel codice agli eventi impostati nel file XAML e imposta la struttura ad albero risultante degli oggetti come contenuto della pagina.

## <a name="loading-xaml-at-runtime"></a>Caricamento di XAML in fase di esecuzione

I [`LoadFromXaml`](xref:Xamarin.Forms.Xaml.Extensions.LoadFromXaml*) metodi sono `public` , quindi possono essere chiamati dalle Xamarin.Forms applicazioni per caricare e analizzare XAML in fase di esecuzione. Questo consente scenari come un'applicazione che Scarica XAML da un servizio Web, creando la vista richiesta dal codice XAML e visualizzandola nell'applicazione.

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

In questo esempio [`Button`](xref:Xamarin.Forms.Button) viene creata un'istanza con il valore della [`Text`](xref:Xamarin.Forms.Button.Text) proprietà impostato dal codice XAML definito in `string` . `Button`Viene quindi aggiunto a un oggetto [`StackLayout`](xref:Xamarin.Forms.StackLayout) definito nel file XAML per la pagina.

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

Il caricamento di XAML in fase di esecuzione con il [`LoadFromXaml`](xref:Xamarin.Forms.Xaml.Extensions.LoadFromXaml*) metodo non consente l'accesso fortemente tipizzato agli elementi XAML che hanno specificato nomi di oggetti di runtime (usando `x:Name` ). Tuttavia, è possibile recuperare questi elementi XAML usando il [`FindByName`](xref:Xamarin.Forms.NameScopeExtensions.FindByName*) metodo e quindi accedervi come richiesto:

```csharp
// See the sample for the full XAML string
string pageXAML = "<?xml version=\"1.0\" encoding=\"utf-8\"?>\r\n<ContentPage xmlns=\"http://xamarin.com/schemas/2014/forms\"\nxmlns:x=\"http://schemas.microsoft.com/winfx/2009/xaml\"\nx:Class=\"LoadRuntimeXAML.CatalogItemsPage\"\nTitle=\"Catalog Items\">\n<StackLayout>\n<Label x:Name=\"monkeyName\"\n />\n</StackLayout>\n</ContentPage>";
ContentPage page = new ContentPage().LoadFromXaml(pageXAML);

Label monkeyLabel = page.FindByName<Label>("monkeyName");
monkeyLabel.Text = "Seated Monkey";
...
```

In questo esempio, il codice XAML per un oggetto [`ContentPage`](xref:Xamarin.Forms.ContentPage) è inflat. Questo codice XAML include un oggetto [`Label`](xref:Xamarin.Forms.Label) denominato `monkeyName` , che viene recuperato utilizzando il [`FindByName`](xref:Xamarin.Forms.NameScopeExtensions.FindByName*) metodo, prima dell'impostazione della relativa [`Text`](xref:Xamarin.Forms.Label.Text) Proprietà.

## <a name="related-links"></a>Collegamenti correlati

- [LoadRuntimeXAML (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xaml-loadruntimexaml)
