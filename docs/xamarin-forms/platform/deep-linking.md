---
title: L'indicizzazione di applicazione e il collegamento completo
description: L'indicizzazione dell'applicazione consente alle applicazioni che, in caso contrario, verrebbero dimenticate dopo qualche utilizza rimanere rilevanti da visualizzati nei risultati della ricerca. Il collegamento completo consente alle applicazioni rispondere a un risultato di ricerca che contiene i dati dell'applicazione, in genere passando a una pagina a cui fa riferimento da un collegamento diretto. In questo articolo viene illustrato come utilizzare l'indicizzazione di applicazione e il collegamento completo per eseguire la ricerca nei dispositivi Android iOS e xamarin. Forms contenuto dell'applicazione.
ms.topic: article
ms.prod: xamarin
ms.assetid: 410C5D19-AA3C-4E0D-B799-E288C5803226
ms.technology: xamarin-forms
ms.custom: xamu-video
author: davidbritch
ms.author: dabritch
ms.date: 07/11/2016
ms.openlocfilehash: 38d3b6da0dd33e038f2d50209280f2983faf6013
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/09/2018
---
# <a name="application-indexing-and-deep-linking"></a>L'indicizzazione di applicazione e il collegamento completo

_L'indicizzazione dell'applicazione consente alle applicazioni che, in caso contrario, verrebbero dimenticate dopo qualche utilizza rimanere rilevanti da visualizzati nei risultati della ricerca. Il collegamento completo consente alle applicazioni rispondere a un risultato di ricerca che contiene i dati dell'applicazione, in genere passando a una pagina a cui fa riferimento da un collegamento diretto. In questo articolo viene illustrato come utilizzare l'indicizzazione di applicazione e il collegamento completo per eseguire la ricerca nei dispositivi Android iOS e xamarin. Forms contenuto dell'applicazione._

> [!VIDEO https://youtube.com/embed/UJv4jUs7cJw]

**Profondità di collegamento con xamarin. Forms e Azure, da [University Xamarin](https://university.xamarin.com/)**


L'indicizzazione di xamarin. Forms applicazione e il collegamento completo forniscono un'API per la pubblicazione di metadati per l'indicizzazione di applicazione di navigazione degli utenti nelle applicazioni. Contenuto indicizzato è quindi possibile cercare per ricerca Spotlight, nella ricerca di Google o in una ricerca sul web. Selezionando un risultato di ricerca che contiene un collegamento diretto verrà generato un evento che può essere gestito da un'applicazione e viene in genere utilizzato per passare alla pagina a cui fa riferimento il collegamento diretto.

L'applicazione di esempio viene illustrata un'applicazione di elenco di attività in cui i dati vengono archiviati in un database locale di SQLite, come illustrato nelle schermate seguenti:

![](deep-linking-images/screenshots.png "Applicazione elenco attività")

Ogni `TodoItem` istanza creata dall'utente viene indicizzato. Ricerca specifici della piattaforma consente quindi di individuare dati indicizzati dall'applicazione. Quando l'utente tocca su un elemento di risultato di ricerca per l'applicazione, l'applicazione viene avviata, il `TodoItemPage` , ci si sposta e `TodoItem` a cui fa riferimento la completo collegamento viene visualizzato.

Per ulteriori informazioni sull'utilizzo di un database SQLite, vedere [utilizzo di un Database locale](~/xamarin-forms/app-fundamentals/databases.md).

> [!NOTE]
> L'indicizzazione di applicazione di xamarin. Forms e completo di funzionalità di collegamento è disponibile solo nelle piattaforme iOS e Android e richiede iOS 9 e API 23 rispettivamente.

## <a name="setup"></a>Configurazione

Le sezioni seguenti forniscono le istruzioni di configurazione aggiuntivi per l'utilizzo di questa funzionalità in cui le piattaforme iOS e Android.

### <a name="ios"></a>iOS

Nella piattaforma iOS, non vi è alcuna impostazione aggiuntiva necessaria per utilizzare questa funzionalità.

### <a name="android"></a>Android

Nella piattaforma Android, esistono alcuni prerequisiti che devono essere soddisfatte per utilizzare l'indicizzazione di applicazione e la funzionalità collegamento diretto:

1. Una versione dell'applicazione deve essere in tempo reale in Google Play.
1. Un sito Web complementare deve essere registrato nei confronti dell'applicazione nella Console per sviluppatori Google. Dopo l'applicazione è associata a un sito Web, gli URL possono essere indicizzate che funzionano per il sito Web sia l'applicazione, che quindi può essere fornita nei risultati della ricerca. Per ulteriori informazioni, vedere [l'indicizzazione di App nella Google ricerca](https://support.google.com/googleplay/android-developer/answer/6041489) nel sito Web di Google.
1. L'applicazione deve supportare i tipi di URL HTTP sul `MainActivity` possibile rispondere (classe), che indicano l'indicizzazione di quali tipi di schemi dati URL applicazione dell'applicazione. Per ulteriori informazioni, vedere [configurazione del filtro della finalità](~/android/platform/app-linking.md#configure-intent-filter).

Una volta soddisfatti questi prerequisiti, la seguente impostazione aggiuntiva è necessaria per utilizzare l'indicizzazione di xamarin. Forms applicazione e il collegamento completo nella piattaforma Android:

1. Installare il [Xamarin.Forms.AppLinks](https://www.nuget.org/packages/Xamarin.Forms.AppLinks/) pacchetto NuGet nel progetto di applicazione Android.
1. Nel `MainActivity.cs` file, importare il `Xamarin.Forms.Platform.Android.AppLinks` dello spazio dei nomi.
1. Nel `MainActivity.OnCreate` eseguire l'override, aggiungere la seguente riga di codice sotto `Forms.Init(this, bundle)`:

```csharp
AndroidAppLinks.Init (this);
```

Per ulteriori informazioni, vedere [complete collegamento contenuto con navigazione URL con xamarin. Forms](https://blog.xamarin.com/deep-link-content-with-xamarin-forms-url-navigation/) sul blog di Xamarin.

## <a name="indexing-a-page"></a>Indicizzazione di una pagina

Il processo per l'indicizzazione di una pagina ed esporlo ad ricerca Google e Spotlight è come segue:

1. Creare un [ `AppLinkEntry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.AppLinkEntry/) che contiene i metadati richiesti per l'indice di pagina, oltre a un collegamento diretto per tornare alla pagina quando l'utente seleziona il contenuto indicizzato nei risultati della ricerca.
1. Registrare il [ `AppLinkEntry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.AppLinkEntry/) istanza da indicizzare per la ricerca.

Esempio di codice riportato di seguito viene illustrato come creare un [ `AppLinkEntry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.AppLinkEntry/) istanza:

```csharp
AppLinkEntry GetAppLink (TodoItem item)
{
  var pageType = GetType ().ToString ();
  var pageLink = new AppLinkEntry {
    Title = item.Name,
    Description = item.Notes,
    AppLinkUri = new Uri (string.Format ("http://{0}/{1}?id={2}",
      App.AppName, pageType, WebUtility.UrlEncode (item.ID)), UriKind.RelativeOrAbsolute),
    IsLinkActive = true,
    Thumbnail = ImageSource.FromFile ("monkey.png")
  };

  return pageLink;
}
```

Il [ `AppLinkEntry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.AppLinkEntry/) istanza contiene un numero di proprietà i cui valori sono necessarie per la pagina di indice e creare un collegamento diretto. Il [ `Title` ](https://developer.xamarin.com/api/property/Xamarin.Forms.IAppLinkEntry.Title/), [ `Description` ](https://developer.xamarin.com/api/property/Xamarin.Forms.IAppLinkEntry.Description/), e [ `Thumbnail` ](https://developer.xamarin.com/api/property/Xamarin.Forms.IAppLinkEntry.Thumbnail/) proprietà vengono utilizzate per identificare il contenuto indicizzato quando viene visualizzato nei risultati della ricerca. Il [ `IsLinkActive` ](https://developer.xamarin.com/api/property/Xamarin.Forms.IAppLinkEntry.IsLinkActive/) è impostata su `true` per indicare che il contenuto indicizzato è attualmente visualizzato. Il [ `AppLinkUri` ](https://developer.xamarin.com/api/property/Xamarin.Forms.IAppLinkEntry.AppLinkUri/) proprietà è un `Uri` che contiene le informazioni necessarie per tornare alla pagina corrente e visualizzare l'oggetto corrente `TodoItem`. Nell'esempio seguente viene illustrato un esempio `Uri` per l'applicazione di esempio:

```csharp
http://deeplinking/DeepLinking.TodoItemPage?id=ec38ebd1-811e-4809-8a55-0d028fce7819
```

Questo `Uri` contiene tutte le informazioni necessarie per avviare il `deeplinking` app, passare al `DeepLinking.TodoItemPage`e visualizzare il `TodoItem` che ha un `ID` di `ec38ebd1-811e-4809-8a55-0d028fce7819`.

## <a name="registering-content-for-indexing"></a>Registrazione per l'indicizzazione di contenuto

Una volta un [ `AppLinkEntry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.AppLinkEntry/) istanza è stata creata, deve essere registrato per l'indicizzazione per essere visualizzati nei risultati della ricerca. Questa operazione viene eseguita con il [ `RegisterLink` ](https://developer.xamarin.com/api/member/Xamarin.Forms.IAppLinks.RegisterLink/p/Xamarin.Forms.IAppLinkEntry/) (metodo), come illustrato nell'esempio di codice seguente:

```csharp
Application.Current.AppLinks.RegisterLink (appLink);
```

Aggiunge il [ `AppLinkEntry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.AppLinkEntry/) istanza per l'applicazione [ `AppLinks` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Application.AppLinks/) insieme.

> [!NOTE]
> Il `RegisterLink` metodo può essere utilizzato anche per aggiornare il contenuto che è stato indicizzato per una pagina.

Una volta un [ `AppLinkEntry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.AppLinkEntry/) istanza è stata registrata per l'indicizzazione, può essere visualizzato nei risultati della ricerca. La schermata seguente mostra il contenuto indicizzato visualizzati nei risultati della ricerca nella piattaforma iOS:

![](deep-linking-images/ios-search.png "Contenuto indicizzato nei risultati della ricerca in iOS")

## <a name="de-registering-indexed-content"></a>Annullare la registrazione di indicizzazione del contenuto

Il [ `DeregisterLink` ](https://developer.xamarin.com/api/member/Xamarin.Forms.IAppLinks.DeregisterLink/p/Xamarin.Forms.IAppLinkEntry/) consente di rimuovere il contenuto indicizzato dai risultati della ricerca, come illustrato nell'esempio di codice seguente:

```csharp
Application.Current.AppLinks.DeregisterLink (appLink);
```

Questa operazione rimuove il [ `AppLinkEntry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.AppLinkEntry/) istanza dell'applicazione [ `AppLinks` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Application.AppLinks/) insieme.

> [!NOTE]
> In Android non è possibile rimuovere contenuto indicizzato dai risultati della ricerca.

<a name="responding" />

## <a name="responding-to-a-deep-link"></a>Risponde a un collegamento completo

Quando viene visualizzato nei risultati della ricerca di contenuto indicizzato e viene selezionato da un utente, il `App` classe per l'applicazione riceverà una richiesta per gestire il `Uri` contenuti nel contenuto indicizzato. Questa richiesta può essere elaborata nel [ `OnAppLinkRequestReceived` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Application.OnAppLinkRequestReceived/p/System.Uri/) esegue l'override, come illustrato nell'esempio di codice seguente:

```csharp
public class App : Application
{
  ...

  protected override async void OnAppLinkRequestReceived (Uri uri)
  {
    string appDomain = "http://" + App.AppName.ToLowerInvariant () + "/";
    if (!uri.ToString ().ToLowerInvariant ().StartsWith (appDomain)) {
      return;
    }

    string pageUrl = uri.ToString ().Replace (appDomain, string.Empty).Trim ();
    var parts = pageUrl.Split ('?');
    string page = parts [0];
    string pageParameter = parts [1].Replace ("id=", string.Empty);

    var formsPage = Activator.CreateInstance (Type.GetType (page));
    var todoItemPage = formsPage as TodoItemPage;
    if (todoItemPage != null) {
      var todoItem = App.Database.Find (pageParameter);
      todoItemPage.BindingContext = todoItem;
      await MainPage.Navigation.PushAsync (formsPage as Page);
    }

    base.OnAppLinkRequestReceived (uri);
  }
}
```

Il [ `OnAppLinkRequestReceived` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Application.OnAppLinkRequestReceived/p/System.Uri/) metodo verifica che i dati ricevuti `Uri` è destinata l'applicazione, prima dell'analisi di `Uri` nella pagina per spostarsi a e il parametro da passare alla pagina. Un'istanza della pagina per spostarsi a viene creato e `TodoItem` rappresentato dalla pagina parametro viene recuperato. Il [ `BindingContext` ](https://developer.xamarin.com/api/property/Xamarin.Forms.BindableObject.BindingContext/) della pagina per spostarsi a viene impostato il `TodoItem`. Ciò garantisce che quando il `TodoItemPage` viene visualizzato per il [ `PushAsync` ](https://developer.xamarin.com/api/member/Xamarin.Forms.INavigation.PushAsync/p/Xamarin.Forms.Page/) (metodo), mostrerà la `TodoItem` il cui `ID` è contenuto nel collegamento diretto.

## <a name="making-content-available-for-search-indexing"></a>Rendendo disponibili per l'indicizzazione di ricerca di contenuto

Ogni volta che viene visualizzata la pagina rappresentata da un collegamento diretto, il [ `AppLinkEntry.IsLinkActive` ](https://developer.xamarin.com/api/property/Xamarin.Forms.IAppLinkEntry.IsLinkActive/) può essere impostata su `true`. In iOS e Android in questo modo il [ `AppLinkEntry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.AppLinkEntry/) istanza disponibile per l'indicizzazione di ricerca e solo per iOS, che consente inoltre di `AppLinkEntry` istanza disponibile per la consegna. Per ulteriori informazioni sul passaggio di consegne, vedere [Introduzione a Handoff](~/ios/platform/handoff.md).

L'esempio di codice seguente viene illustrato come impostare il [ `AppLinkEntry.IsLinkActive` ](https://developer.xamarin.com/api/property/Xamarin.Forms.IAppLinkEntry.IsLinkActive/) proprietà `true` nel [ `Page.OnAppearing` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Page.OnAppearing()/) override:

```csharp
protected override void OnAppearing ()
{
  appLink = GetAppLink (BindingContext as TodoItem);
  if (appLink != null) {
    appLink.IsLinkActive = true;
  }
}
```

Analogamente, quando la pagina rappresentata da un collegamento diretto ci si sposta, la [ `AppLinkEntry.IsLinkActive` ](https://developer.xamarin.com/api/property/Xamarin.Forms.IAppLinkEntry.IsLinkActive/) può essere impostata su `false`. In iOS e Android, viene arrestata la [ `AppLinkEntry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.AppLinkEntry/) istanza viene annunciato anche per l'indicizzazione di ricerca e in iOS solo, si arresta la pubblicità il `AppLinkEntry` istanza per la consegna. Questo può essere eseguito nel [ `Page.OnDisappearing` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Page.OnDisappearing()/) esegue l'override, come illustrato nell'esempio di codice seguente:

```csharp
protected override void OnDisappearing ()
{
  if (appLink != null) {
    appLink.IsLinkActive = false;
  }
}
```

## <a name="providing-data-to-handoff"></a>Fornisce dati per la consegna

In iOS, dati specifici dell'applicazione possono essere archiviati durante l'indicizzazione della pagina. Questo risultato viene ottenuto mediante l'aggiunta di dati per il [ `KeyValues` ](https://developer.xamarin.com/api/property/Xamarin.Forms.IAppLinkEntry.KeyValues/) raccolta, ovvero un `Dictionary<string, string>` per l'archiviazione di coppie chiave-valore che vengono utilizzate nel passaggio di consegne. Handoff consente all'utente di avviare un'attività su uno dei propri dispositivi e continuare l'attività in un altro dei propri dispositivi (come identificato dall'account iCloud dell'utente). Il codice seguente viene illustrato un esempio di archiviare coppie chiave-valore specifico dell'applicazione:

```csharp
var pageLink = new AppLinkEntry {
  ...  
};
pageLink.KeyValues.Add("appName", App.AppName);
pageLink.KeyValues.Add("companyName", "Xamarin");
```

I valori archiviati nel [ `KeyValues` ](https://developer.xamarin.com/api/property/Xamarin.Forms.IAppLinkEntry.KeyValues/) raccolta verrà archiviata nei metadati per la pagina indicizzata e verrà ripristinata quando l'utente tocca un risultato di ricerca che contiene un collegamento diretto (o quando Handoff viene utilizzato per visualizzare il contenuto in un altro connesso dispositivo).

Inoltre, i valori per le chiavi seguenti possono essere specificati:

- `contentType` -un `string` che specifica l'identificatore di tipo uniforme del contenuto indicizzato. Le convenzioni consigliate da utilizzare per questo valore sono il nome del tipo della pagina che contiene il contenuto indicizzato.
- `associatedWebPage` -un `string` che rappresenta la pagina web da visitare se il contenuto indicizzato può essere visualizzato anche nel web o se l'applicazione supporta i collegamenti diretti del Safari.
- `shouldAddToPublicIndex` -un `string` tra `true` o `false` che controlla se aggiungere il contenuto indicizzato all'indice di cloud pubblico di Apple, che può quindi essere visualizzati agli utenti che non hanno installato l'applicazione nel proprio dispositivo iOS o meno. Tuttavia, solo perché il contenuto è stato impostato per l'indicizzazione pubblico, non significa che verrà automaticamente aggiunto all'indice di cloud pubblico di Apple. Per ulteriori informazioni, vedere [pubblica l'indicizzazione di ricerca](~/ios/platform/search/nsuseractivity.md). Si noti che questa chiave deve essere impostata su `false` durante l'aggiunta dei dati personali di [ `KeyValues` ](https://developer.xamarin.com/api/property/Xamarin.Forms.IAppLinkEntry.KeyValues/) insieme.

> [!NOTE]
> Il `KeyValues` raccolta non viene utilizzata con la piattaforma Android.

Per ulteriori informazioni sul passaggio di consegne, vedere [Introduzione a Handoff](~/ios/platform/handoff.md).

## <a name="summary"></a>Riepilogo

In questo articolo è illustrato come utilizzare l'indicizzazione di applicazione e il collegamento completo per eseguire la ricerca nei dispositivi Android iOS e xamarin. Forms contenuto dell'applicazione. L'indicizzazione dell'applicazione consente alle applicazioni di rimanere rilevanti da visualizzati nei risultati di ricerca che in caso contrario potrebbero essere dimenticati dopo l'utilizzo di alcune.


## <a name="related-links"></a>Collegamenti correlati

- [Collegamento completo (esempio)](https://developer.xamarin.com/samples/xamarin-forms/deeplinking/)
- [API di ricerca iOS](~/ios/platform/search/index.md)
- [Collegamento App in Android 6.0](~/android/platform/app-linking.md)
- [AppLinkEntry](https://developer.xamarin.com/api/type/Xamarin.Forms.AppLinkEntry/)
- [IAppLinkEntry](https://developer.xamarin.com/api/type/Xamarin.Forms.IAppLinkEntry/)
- [IAppLinks](https://developer.xamarin.com/api/type/Xamarin.Forms.IAppLinks/)
