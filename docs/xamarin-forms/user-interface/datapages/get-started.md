---
title: "Introduzione con le pagine di dati" Descrizione: "in questo articolo viene illustrato come iniziare a creare una semplice pagina basata sui dati utilizzando le pagine di dati Xamarin.Forms ".
ms. prod: Novell MS. AssetID: 6416E5FA-6384-4298-BAA1-A89381E47210 ms. Technology: Novell-Forms Author: davidbritch ms. Author: dabritch ms. Date: 12/01/2017 no-loc: [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="getting-started-with-datapages"></a>Introduzione con le pagine Web

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://github.com/xamarin/xamarin-forms-samples/tree/master/Pages/DataPagesDemo)

![](~/media/shared/preview.png "This API is currently in preview")

> [!IMPORTANT]
> Per il rendering delle pagine Web è necessario un Xamarin.Forms riferimento al tema. Questa operazione comporta l'installazione di [ Xamarin.Forms . Tema.](https://www.nuget.org/packages/Xamarin.Forms.Theme.Base/) pacchetto NuGet di base nel progetto, seguito da [ Xamarin.Forms . Theme. Light](https://www.nuget.org/packages/Xamarin.Forms.Theme.Light/) o [ Xamarin.Forms . Pacchetti NuGet Theme. Dark](https://www.nuget.org/packages/Xamarin.Forms.Theme.Dark/) .

Per iniziare a creare una semplice pagina basata sui dati usando l'anteprima di DataPages, seguire questa procedura. Questa demo usa uno stile hardcoded ("Events") nelle compilazioni di anteprima che funziona solo con il formato JSON specifico nel codice.

[![](get-started-images/demo-sml.png "DataPages Sample Application")](get-started-images/demo.png#lightbox "DataPages Sample Application")

## <a name="1-add-nuget-packages"></a>1. aggiungere pacchetti NuGet

Aggiungere i pacchetti NuGet alla Xamarin.Forms libreria .NET standard e ai progetti di applicazione:

- Xamarin.Forms. Pagine
- Xamarin.Forms. Theme. base
- Un'implementazione del tema NuGet (ad esempio, Xamarin.Forms. Theme. Light)

## <a name="2-add-theme-reference"></a>2. Aggiungi riferimento al tema

Nel file **app. XAML** aggiungere un oggetto personalizzato `xmlns:mytheme` per il tema e assicurarsi che il tema venga Unito nel dizionario risorse dell'applicazione:

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
> È anche necessario seguire i passaggi per [caricare gli assembly del tema (di seguito)](#troubleshooting) aggiungendo codice standard a iOS `AppDelegate` e Android `MainActivity` . Questa funzionalità verrà migliorata in una versione di anteprima futura.

## <a name="3-add-a-xaml-page"></a>3. aggiungere una pagina XAML

Aggiungere una nuova pagina XAML all' Xamarin.Forms applicazione e *modificare la classe di base* da `ContentPage` a `Xamarin.Forms.Pages.ListDataPage` . Questa operazione deve essere eseguita sia in C# sia in XAML:

**File C#**

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

Oltre a modificare l'elemento radice nello `<p:ListDataPage>` spazio dei nomi personalizzato per `xmlns:p` , è necessario aggiungere anche:

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

Modificare il `App` costruttore della classe in modo che `MainPage` sia impostato su un oggetto `NavigationPage` contenente la nuova classe `SessionDataPage` . È *necessario* utilizzare una pagina di navigazione.

```csharp
MainPage = new NavigationPage (new SessionDataPage ());
```

## <a name="3-add-the-datasource"></a>3. aggiungere l'origine dati

Eliminare l' `Content` elemento e sostituirlo con un `p:ListDataPage.DataSource` per popolare la pagina con i dati. Nell'esempio seguente viene caricato un file di dati JSON remoto da un URL.

> [!NOTE]
> L'anteprima *richiede* un `StyleClass` attributo per fornire hint di rendering per l'origine dati. `StyleClass="Events"`Fa riferimento a un layout predefinito nell'anteprima e contiene gli stili *hardcoded* in modo da corrispondere all'origine dati JSON utilizzata.

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

I passaggi precedenti dovrebbero avere come risultato una pagina di dati di lavoro:

[![](get-started-images/demo-sml.png "DataPages Sample Application")](get-started-images/demo.png#lightbox "DataPages Sample Application")

Questa operazione funziona perché lo stile predefinito **"Events"** è presente nel pacchetto NuGet tema chiaro ed è definito stili che corrispondono all'origine dati, ad esempio "title", "image", "Presenter").

Gli "eventi" `StyleClass` vengono compilati per visualizzare il `ListDataPage` controllo con un `CardView` controllo personalizzato definito in Xamarin.Forms . Pagine. Il `CardView` controllo dispone di tre proprietà: `ImageSource` , `Text` e `Detail` . Il tema è hardcoded per associare i tre campi dell'origine dati (dal file JSON) a queste proprietà per la visualizzazione.

## <a name="5-customize"></a>5. personalizzare

È possibile eseguire l'override dello stile ereditato specificando un modello e utilizzando le associazioni all'origine dati. Il codice XAML seguente dichiara un modello personalizzato per ogni riga usando la nuova `ListItemControl` `{p:DataSourceBinding}` sintassi e inclusa in ** Xamarin.Forms . Pagine** NuGet:

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

Fornendo un oggetto `DataTemplate` , questo codice esegue l'override di `StyleClass` e usa invece il layout predefinito per un oggetto `ListItemControl` .

[![](get-started-images/custom-sml.png "DataPages Sample Application")](get-started-images/custom.png#lightbox "DataPages Sample Application")

Gli sviluppatori che preferiscono C# a XAML possono anche creare associazioni di origini dati (ricordarsi di includere un' `using Xamarin.Forms.Pages;` istruzione):

```csharp
SetBinding (TitleProperty, new DataSourceBinding ("title"));
```

La creazione di temi da zero è un po' più semplice, ma le future versioni di anteprima faciliteranno questa operazione.

## <a name="troubleshooting"></a>Risoluzione dei problemi

## <a name="could-not-load-file-or-assembly-xamarinformsthemelight-or-one-of-its-dependencies"></a>Non è stato possibile caricare il file o l'assembly ' Xamarin.Forms . Theme. Light ' o una delle relative dipendenze

Nella versione di anteprima, i temi potrebbero non essere in grado di caricare in fase di esecuzione. Per correggere l'errore, aggiungere il codice riportato di seguito ai progetti pertinenti.

**iOS**

In **AppDelegate.cs** aggiungere le righe seguenti dopo`LoadApplication`

```csharp
var x = typeof(Xamarin.Forms.Themes.DarkThemeResources);
x = typeof(Xamarin.Forms.Themes.LightThemeResources);
x = typeof(Xamarin.Forms.Themes.iOS.UnderlineEffect);
```

**Android**

In **MainActivity.cs** aggiungere le righe seguenti dopo`LoadApplication`

```csharp
var x = typeof(Xamarin.Forms.Themes.DarkThemeResources);
x = typeof(Xamarin.Forms.Themes.LightThemeResources);
x = typeof(Xamarin.Forms.Themes.Android.UnderlineEffect);
```

## <a name="related-links"></a>Collegamenti correlati

- [Esempio DataPagesDemo](https://github.com/xamarin/xamarin-forms-samples/tree/master/Pages/DataPagesDemo)
