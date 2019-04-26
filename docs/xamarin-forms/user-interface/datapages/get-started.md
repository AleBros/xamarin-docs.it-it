---
title: Introduzione a DataPages
description: Questo articolo illustra come iniziare a creare una pagina semplice basato sui dati usando DataPages xamarin. Forms.
ms.prod: xamarin
ms.assetid: 6416E5FA-6384-4298-BAA1-A89381E47210
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/01/2017
ms.openlocfilehash: e3256787c0bc0852275f663772b8a91a6825a0dd
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61250680"
---
# <a name="getting-started-with-datapages"></a>Introduzione a DataPages

[![Scaricare l'esempio](~/media/shared/download.png) scaricare l'esempio](https://github.com/xamarin/xamarin-forms-samples/tree/master/Pages/DataPagesDemo)

![](~/media/shared/preview.png "Questa API è attualmente in anteprima")

> [!IMPORTANT]
> DataPages richiede un [tema di xamarin. Forms](~/xamarin-forms/user-interface/themes/index.md) riferimento per il rendering.


Per iniziare a creare una pagina semplice basato sui dati usando l'anteprima DataPages, attenersi alla procedura seguente. Usi questa demo uno stile hardcoded ("eventi") nell'anteprima di compilazioni che funziona solo con il formato JSON specifico nel codice.

[![](get-started-images/demo-sml.png "Applicazione di esempio DataPages")](get-started-images/demo.png#lightbox "applicazione di esempio DataPages")

## <a name="1-add-nuget-packages"></a>1. Aggiungere i pacchetti NuGet

Aggiungere i pacchetti Nuget per i progetti di libreria e applicazione xamarin. Forms .NET Standard:

* Xamarin.Forms.Pages
* Xamarin.Forms.Theme.Base
* Un'implementazione di tema Nuget (ad es. Xamarin.Forms.Theme.Light)

## <a name="2-add-theme-reference"></a>2. Aggiungere il riferimento di tema

Nel **app. XAML** , aggiungere una classe personalizzata `xmlns:mytheme` per il tema e verificare che il tema viene unito nel dizionario risorse dell'applicazione:

```xaml
<Application xmlns="http://xamarin.com/schemas/2014/forms"
  xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
  xmlns:mytheme="clr-namespace:Xamarin.Forms.Themes;assembly=Xamarin.Forms.Theme.Light"
  x:Class="DataPagesDemo.App">
    <Application.Resources>
        <ResourceDictionary MergedWith="mytheme:LightThemeResources" />
    </Application.Resources>
</Application>
```

**IMPORTANTE:** È anche consigliabile seguire i passaggi necessari per [caricare gli assembly di tema (sotto)](#loadtheme) aggiungendo codice boilerplate per iOS `AppDelegate` e Android `MainActivity`. Questo verrà migliorato in una versione futura versione di anteprima.


## <a name="3-add-a-xaml-page"></a>3. Aggiungere una pagina XAML

Aggiungere una nuova pagina XAML per l'applicazione xamarin. Forms, e *modificare la classe di base* dalla `ContentPage` a `Xamarin.Forms.Pages.ListDataPage`. Questo deve essere eseguita in c# e il XAML:

**File c#**

```csharp
public partial class SessionDataPage : Xamarin.Forms.Pages.ListDataPage // was ContentPage
{
  public SessionDataPage ()
  {
    InitializeComponent ();
  }
}
```

**File XAML**

Oltre a modificare l'elemento radice `<p:ListDataPage>` lo spazio dei nomi personalizzato per `xmlns:p` devono essere anche aggiunti:

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<p:ListDataPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:p="clr-namespace:Xamarin.Forms.Pages;assembly=Xamarin.Forms.Pages"
             x:Class="DataPagesDemo.SessionDataPage">

    <ContentPage.Content></ContentPage.Content>

</p:ListDataPage>
```

**Sottoclasse dell'applicazione**

Modifica il `App` costruttore di classe in modo che il `MainPage` è impostata su un `NavigationPage` che contiene il nuovo `SessionDataPage`. Una pagina di navigazione *necessario* utilizzabile.

```csharp
MainPage = new NavigationPage (new SessionDataPage ());
```

## <a name="3-add-the-datasource"></a>3. Aggiungere l'origine dati

Eliminare il `Content` elemento e sostituirlo con un `p:ListDataPage.DataSource` per popolare la pagina con i dati. Nell'esempio seguente un oggetto Json remoto i file di dati viene caricato da un URL.

**Nota:** l'anteprima *richiede* un `StyleClass` attributo fornire suggerimenti per il rendering per l'origine dati. Il `StyleClass="Events"` fa riferimento a un layout in cui è già definito nell'anteprima e contiene gli stili *hardcoded* in base all'origine dei dati JSON in uso.

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<p:ListDataPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:p="clr-namespace:Xamarin.Forms.Pages;assembly=Xamarin.Forms.Pages"
             x:Class="DataPagesDemo.SessionDataPage"
             Title="Sessions" StyleClass="Events">

    <p:ListDataPage.DataSource>
        <p:JsonDataSource Source="http://demo3143189.mockable.io/sessions" />
    </p:ListDataPage.DataSource>

</p:ListDataPage>
```

**Dati JSON**

Un esempio dei dati JSON dal [origine demo](http://demo3143189.mockable.io/sessions) è illustrato di seguito:

```json
[{
  "end": "2016-04-27T18:00:00Z",
  "start": "2016-04-27T17:15:00Z",
  "abstract": "The new Apple TV has been released, and YOU can be one of the first developers to write apps for it. To make things even better, you can build these apps in C#! This session will introduce the basics of how to create a tvOS app with Xamarin, including: differences between tvOS and iOS APIs, TV user interface best practices, responding to user input, as well as the capabilities and limitations of building apps for a television. Grab some popcorn—this is going to be good!",
  "title": "As Seen On TV … Bringing C# to the Living Room",
  "presenter": "Matthew Soucoup",
  "biography": "Matthew is a Xamarin MVP and Certified Xamarin Developer from Madison, WI. He founded his company Code Mill Technologies and started the Madison Mobile .Net Developers Group.  Matt regularly speaks on .Net and Xamarin development at user groups, code camps and conferences throughout the Midwest. Matt gardens hot peppers, rides bikes, and loves Wisconsin micro-brews and cheese.",
  "image": "http://i.imgur.com/ASj60DP.jpg",
  "avatar": "http://i.imgur.com/ASj60DP.jpg",
  "room": "Crick"
}]
```

## <a name="4-run"></a>4. Correre!

I passaggi precedenti dovrebbero comportare una pagina di dati di utilizzo:

[![](get-started-images/demo-sml.png "Applicazione di esempio DataPages")](get-started-images/demo.png#lightbox "applicazione di esempio DataPages")

Questo procedimento funziona perché lo stile preesistente **"Eventi"** esista nel pacchetto Nuget di tema chiaro e ha gli stili definiti che corrispondono all'origine dati (ad es. "title", "immagine", "presenter").

"Eventi" `StyleClass` è stato creato per visualizzare il `ListDataPage` controllo con un oggetto personalizzato `CardView` controllo Xamarin.Forms.Pages definite in. Il `CardView` controllo dispone di tre proprietà: `ImageSource`, `Text`, e `Detail`. Il tema è hardcoded per associare tre campi dell'origine dati (dal file JSON) per queste proprietà per la visualizzazione.

## <a name="5-customize"></a>5. Personalizza

Lo stile ereditato può essere sostituito specificando un modello e utilizzando associazioni all'origine dati. il XAML riportato di seguito viene dichiarato un modello personalizzato per ogni riga usando le nuove `ListItemControl` e `{p:DataSourceBinding}` sintassi che è incluso nel **Xamarin.Forms.Pages** Nuget:

```xaml
<p:ListDataPage.DefaultItemTemplate>
    <DataTemplate>
        <ViewCell>
            <p:ListItemControl
                Title="{p:DataSourceBinding title}"
                Detail="{p:DataSourceBinding room}"
                ImageSource="{p:DataSourceBinding image}"
                DataSource="{Binding Value}"
                HeightRequest="90"
            >
            </p:ListItemControl>
        </ViewCell>
    </DataTemplate>
</p:ListDataPage.DefaultItemTemplate>
```

Fornendo una `DataTemplate` questo codice ignora il `StyleClass` e Usa invece il layout predefinito per un `ListItemControl`.

[![](get-started-images/custom-sml.png "Applicazione di esempio DataPages")](get-started-images/custom.png#lightbox "applicazione di esempio DataPages")

Gli sviluppatori che preferiscono c# a XAML può creare dati di origine associazioni troppo (ricordare di includere un `using Xamarin.Forms.Pages;` istruzione):

```csharp
SetBinding (TitleProperty, new DataSourceBinding ("title"));
```


È un po' più lavoro da creare temi da zero (vedere la [Guida per i temi](~/xamarin-forms/user-interface/themes/index.md)) ma versioni di anteprima future per facilitare questo scopo.


## <a name="troubleshooting"></a>Risoluzione dei problemi

<a name="loadtheme" />

## <a name="could-not-load-file-or-assembly-xamarinformsthemelight-or-one-of-its-dependencies"></a>Non è stato possibile caricare file o l'assembly 'Xamarin.Forms.Theme.Light' o una delle relative dipendenze

Nella versione di anteprima, i temi potrebbero non essere in grado di caricare in fase di esecuzione. Aggiungere il codice seguente nei progetti appropriati per correggere l'errore.

**iOS**

Nel **AppDelegate.cs** aggiungere le righe seguenti dopo `LoadApplication`

```csharp
var x = typeof(Xamarin.Forms.Themes.DarkThemeResources);
x = typeof(Xamarin.Forms.Themes.LightThemeResources);
x = typeof(Xamarin.Forms.Themes.iOS.UnderlineEffect);
```

**Android**

Nel **MainActivity.cs** aggiungere le righe seguenti dopo `LoadApplication`

```csharp
var x = typeof(Xamarin.Forms.Themes.DarkThemeResources);
x = typeof(Xamarin.Forms.Themes.LightThemeResources);
x = typeof(Xamarin.Forms.Themes.Android.UnderlineEffect);
```



## <a name="related-links"></a>Collegamenti correlati

- [Esempio DataPagesDemo](https://github.com/xamarin/xamarin-forms-samples/tree/master/Pages/DataPagesDemo)
