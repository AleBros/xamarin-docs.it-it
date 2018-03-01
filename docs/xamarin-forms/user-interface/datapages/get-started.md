---
title: Introduzione a DataPages
ms.topic: article
ms.prod: xamarin
ms.assetid: 6416E5FA-6384-4298-BAA1-A89381E47210
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/01/2017
ms.openlocfilehash: 79ce4a1393b2affade3422f73e1c3d4680d199df
ms.sourcegitcommit: 61f5ecc5a2b5dcfbefdef91664d7460c0ee2f357
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/28/2018
---
# <a name="getting-started-with-datapages"></a>Introduzione a DataPages

![](~/media/shared/preview.png "Questa API è attualmente in anteprima")

> [!IMPORTANT]
> DataPages richiede un [xamarin. Forms tema](~/xamarin-forms/user-interface/themes/index.md) riferimento per il rendering.


Per iniziare la creazione di una pagina di unità di dati semplice utilizzando l'anteprima DataPages, attenersi alla procedura seguente. Utilizza questa demo uno stile hardcoded ("eventi") nel riquadro di anteprima compilazioni che funziona solo con il formato JSON specifico nel codice.

[ ![](get-started-images/demo-sml.png "Applicazione di esempio DataPages")](get-started-images/demo.png "applicazione di esempio DataPages")

## <a name="1-add-nuget-packages"></a>1. Aggiungere pacchetti NuGet

Aggiungere questi pacchetti Nuget per la libreria di classi Portabile xamarin. Forms e i progetti di applicazione:

* Xamarin.Forms.Pages
* Xamarin.Forms.Theme.Base
* Implementazione di un tema Nuget (ad es. Xamarin.Forms.Themes.Light)

## <a name="2-add-theme-reference"></a>2. Aggiungere il riferimento del tema

Nel **app** file, aggiunge un oggetto personalizzato `xmlns:mytheme` per il tema e assicurarsi che il tema viene unito nel dizionario di risorse dell'applicazione:

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

**Importante:** è necessario seguire anche i passaggi per [caricare gli assembly di tema (sotto)](#loadtheme) aggiungendo codice boilerplate per iOS `AppDelegate` e Android `MainActivity`. Questo verrà migliorato in una versione futura di anteprima.


## <a name="3-add-a-xaml-page"></a>3. Aggiungere una pagina XAML

Aggiungere una nuova pagina XAML all'applicazione, xamarin. Forms e *modificare la classe base* da `ContentPage` a `Xamarin.Forms.Pages.ListDataPage`. Questa operazione deve essere eseguita nei linguaggi c# e XAML:

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

Oltre a modificare l'elemento radice per `<p:ListDataPage>` lo spazio dei nomi personalizzato per `xmlns:p` deve inoltre essere aggiunto:

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

Modifica il `App` costruttore della classe in modo che il `MainPage` è impostata su un `NavigationPage` che contiene il nuovo `SessionDataPage`. Una pagina di spostamento *deve* utilizzabile.

```csharp
MainPage = new NavigationPage (new SessionDataPage ());
```

## <a name="3-add-the-datasource"></a>3. Aggiungere l'origine dati

Eliminare il `Content` elemento e sostituirlo con un `p:ListDataPage.DataSource` per popolare la pagina con i dati. Nell'esempio seguente un Json remoto è caricamento del file di dati da un URL.

**Nota:** l'anteprima *richiede* un `StyleClass` attributo di fornire suggerimenti per il rendering per l'origine dati. Il `StyleClass="Events"` fa riferimento a un layout in cui è già definito nell'anteprima e contiene gli stili *hardcoded* in base all'origine dati JSON in uso.

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

Un esempio dei dati JSON di [origine demo](http://demo3143189.mockable.io/sessions) è illustrato di seguito:

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

I passaggi precedenti devono risultare in una pagina di dati di utilizzo:

[ ![](get-started-images/demo-sml.png "Applicazione di esempio DataPages")](get-started-images/demo.png "applicazione di esempio DataPages")

Questo procedimento funziona perché lo stile predefinito **"Eventi"** esista nel pacchetto Nuget di tema chiaro e con gli stili definiti che corrispondano all'origine dati (ad es. "title", "image", "presenter").

Gli eventi"" `StyleClass` è compilato per la visualizzazione di `ListDataPage` controllo con un oggetto personalizzato `CardView` controllo Xamarin.Forms.Pages definito in. Il `CardView` controllo dispone di tre proprietà: `ImageSource`, `Text`, e `Detail`. Il tema è hardcoded per associare i tre campi dell'origine dati (dal file JSON) per queste proprietà per la visualizzazione.

## <a name="5-customize"></a>5. Personalizza

Stile ereditate possa eseguire l'override specificando un modello e utilizzare associazioni all'origine dati. Il codice XAML seguente dichiara un modello personalizzato per ogni riga utilizzando il nuovo `ListItemControl` e `{p:DataSourceBinding}` sintassi che fa il **Xamarin.Forms.Pages** Nuget:

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

Fornendo un `DataTemplate` questo codice esegue l'override di `StyleClass` e utilizza il layout predefinito per un `ListItemControl`.

[ ![](get-started-images/custom-sml.png "Applicazione di esempio DataPages")](get-started-images/custom.png "applicazione di esempio DataPages")

Gli sviluppatori che preferiscono c# in XAML può creare dati di origine associazioni troppo (ricordarsi di includere un `using Xamarin.Forms.Pages;` istruzione):

```csharp
SetBinding (TitleProperty, new DataSourceBinding ("title"));
```


È un po' più lavoro per creare temi da zero (vedere il [Guida temi](~/xamarin-forms/user-interface/themes/index.md)) ma versioni di anteprima future di questo più semplici da eseguire.


## <a name="troubleshooting"></a>Risoluzione dei problemi

<a name="loadtheme"/>

## <a name="could-not-load-file-or-assembly-xamarinformsthemelight-or-one-of-its-dependencies"></a>Non è stato possibile caricare file o l'assembly 'Xamarin.Forms.Theme.Light' o una delle relative dipendenze

Nella versione di anteprima, temi potrebbero non essere in grado di caricare in fase di esecuzione. Aggiungere il codice riportato di seguito nei progetti rilevanti per correggere l'errore.

**iOS**

Nel **appdelegate. cs** aggiungere le seguenti righe dopo `LoadApplication`

```csharp
var x = typeof(Xamarin.Forms.Themes.DarkThemeResources);
x = typeof(Xamarin.Forms.Themes.LightThemeResources);
x = typeof(Xamarin.Forms.Themes.iOS.UnderlineEffect);
```

**Android**

Nel **Mainactivity** aggiungere le seguenti righe dopo `LoadApplication`

```csharp
var x = typeof(Xamarin.Forms.Themes.DarkThemeResources);
x = typeof(Xamarin.Forms.Themes.LightThemeResources);
x = typeof(Xamarin.Forms.Themes.Android.UnderlineEffect);
```



## <a name="related-links"></a>Collegamenti correlati

- [Esempio DataPagesDemo](https://github.com/xamarin/xamarin-forms-samples/tree/master/Pages/DataPagesDemo)
