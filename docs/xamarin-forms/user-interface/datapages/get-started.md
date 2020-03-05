---
title: Introduzione a DataPages
description: Questo articolo illustra come iniziare a creare una pagina semplice basato sui dati usando DataPages xamarin. Forms.
ms.prod: xamarin
ms.assetid: 6416E5FA-6384-4298-BAA1-A89381E47210
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/01/2017
ms.openlocfilehash: 1f7917784ea66c31979b87f43639a7d03756692c
ms.sourcegitcommit: db422e33438f1b5c55852e6942c3d1d75dc025c4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/24/2020
ms.locfileid: "78292584"
---
# <a name="getting-started-with-datapages"></a>Introduzione a DataPages

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://github.com/xamarin/xamarin-forms-samples/tree/master/Pages/DataPagesDemo)

![](~/media/shared/preview.png "This API is currently in preview")

> [!IMPORTANT]
> Per il rendering delle pagine Web è necessario un riferimento al tema Novell. Forms. Ciò comporta l'installazione del pacchetto NuGet [Novell. Forms. Theme. base](https://www.nuget.org/packages/Xamarin.Forms.Theme.Base/) nel progetto, seguito dai pacchetti NuGet [Novell. Forms. Theme. Light](https://www.nuget.org/packages/Xamarin.Forms.Theme.Light/) o [Novell. Forms. Theme. Dark](https://www.nuget.org/packages/Xamarin.Forms.Theme.Dark/) .

Per iniziare a creare una pagina semplice basato sui dati usando l'anteprima DataPages, attenersi alla procedura seguente. Usi questa demo uno stile hardcoded ("eventi") nell'anteprima di compilazioni che funziona solo con il formato JSON specifico nel codice.

[![](get-started-images/demo-sml.png "DataPages Sample Application")](get-started-images/demo.png#lightbox "DataPages Sample Application")

## <a name="1-add-nuget-packages"></a>1. aggiungere pacchetti NuGet

Aggiungere i pacchetti NuGet ai progetti Novell. Forms .NET Standard Library e Application:

- Xamarin.Forms.Pages
- Xamarin.Forms.Theme.Base
- Un'implementazione del tema NuGet (ad esempio, Novell. Forms. Theme. Light)

## <a name="2-add-theme-reference"></a>2. Aggiungi riferimento al tema

Nel file **app. XAML** aggiungere un `xmlns:mytheme` personalizzato per il tema e assicurarsi che il tema venga Unito nel dizionario risorse dell'applicazione:

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

> [!IMPORTANT]
> È anche necessario seguire i passaggi per [caricare gli assembly del tema (di seguito)](#loadtheme) aggiungendo un codice standard ai `MainActivity`iOS `AppDelegate` e Android. Questo verrà migliorato in una versione futura versione di anteprima.

## <a name="3-add-a-xaml-page"></a>3. aggiungere una pagina XAML

Aggiungere una nuova pagina XAML all'applicazione Novell. Forms e *modificare la classe di base* da `ContentPage` a `Xamarin.Forms.Pages.ListDataPage`. Questo deve essere eseguita in c# e il XAML:

**C#file**

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

Oltre a modificare l'elemento radice in `<p:ListDataPage>` è necessario aggiungere anche lo spazio dei nomi personalizzato per `xmlns:p`:

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

Modificare il costruttore della classe `App` in modo che l'`MainPage` sia impostato su un `NavigationPage` contenente la nuova `SessionDataPage`. È *necessario* utilizzare una pagina di navigazione.

```csharp
MainPage = new NavigationPage (new SessionDataPage ());
```

## <a name="3-add-the-datasource"></a>3. aggiungere l'origine dati

Eliminare l'elemento `Content` e sostituirlo con un `p:ListDataPage.DataSource` per popolare la pagina con i dati. Nell'esempio seguente un oggetto Json remoto i file di dati viene caricato da un URL.

> [!NOTE]
> L'anteprima *richiede* un attributo `StyleClass` per fornire hint di rendering per l'origine dati. Il `StyleClass="Events"` si riferisce a un layout predefinito nell'anteprima e contiene gli stili *hardcoded* in modo da corrispondere all'origine dati JSON utilizzata.

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

Di seguito è riportato un esempio dei dati JSON dall'origine demo:

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

## <a name="4-run"></a>4. eseguire

I passaggi precedenti dovrebbero comportare una pagina di dati di utilizzo:

[![](get-started-images/demo-sml.png "DataPages Sample Application")](get-started-images/demo.png#lightbox "DataPages Sample Application")

Questa operazione funziona perché lo stile predefinito **"Events"** è presente nel pacchetto NuGet tema chiaro ed è definito stili che corrispondono all'origine dati, ad esempio "title", "immagine", "presenter").

Il `StyleClass` "Events" viene compilato per visualizzare il controllo `ListDataPage` con un controllo `CardView` personalizzato definito in Novell. Forms. Pages. Il controllo `CardView` dispone di tre proprietà: `ImageSource`, `Text`e `Detail`. Il tema è hardcoded per associare tre campi dell'origine dati (dal file JSON) per queste proprietà per la visualizzazione.

## <a name="5-customize"></a>5. personalizzare

Lo stile ereditato può essere sostituito specificando un modello e utilizzando associazioni all'origine dati. Il codice XAML seguente dichiara un modello personalizzato per ogni riga usando la nuova `ListItemControl` e `{p:DataSourceBinding}` sintassi inclusa in **Novell. Forms. Pages** NuGet:

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

Fornendo un `DataTemplate` questo codice esegue l'override del `StyleClass` e usa invece il layout predefinito per un `ListItemControl`.

[![](get-started-images/custom-sml.png "DataPages Sample Application")](get-started-images/custom.png#lightbox "DataPages Sample Application")

Gli sviluppatori che C# preferiscono XAML possono anche creare associazioni di origini dati (ricordare di includere un'istruzione `using Xamarin.Forms.Pages;`):

```csharp
SetBinding (TitleProperty, new DataSourceBinding ("title"));
```

La creazione di temi da zero è un po' più semplice, ma le future versioni di anteprima faciliteranno questa operazione.

## <a name="troubleshooting"></a>Risoluzione dei problemi

<a name="loadtheme" />

## <a name="could-not-load-file-or-assembly-xamarinformsthemelight-or-one-of-its-dependencies"></a>Non è stato possibile caricare file o l'assembly 'Xamarin.Forms.Theme.Light' o una delle relative dipendenze

Nella versione di anteprima, i temi potrebbero non essere in grado di caricare in fase di esecuzione. Aggiungere il codice seguente nei progetti appropriati per correggere l'errore.

**iOS**

In **AppDelegate.cs** aggiungere le righe seguenti dopo `LoadApplication`

```csharp
var x = typeof(Xamarin.Forms.Themes.DarkThemeResources);
x = typeof(Xamarin.Forms.Themes.LightThemeResources);
x = typeof(Xamarin.Forms.Themes.iOS.UnderlineEffect);
```

**Android**

In **MainActivity.cs** aggiungere le righe seguenti dopo `LoadApplication`

```csharp
var x = typeof(Xamarin.Forms.Themes.DarkThemeResources);
x = typeof(Xamarin.Forms.Themes.LightThemeResources);
x = typeof(Xamarin.Forms.Themes.Android.UnderlineEffect);
```

## <a name="related-links"></a>Collegamenti correlati

- [Esempio DataPagesDemo](https://github.com/xamarin/xamarin-forms-samples/tree/master/Pages/DataPagesDemo)
