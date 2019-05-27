---
title: Indicizzazione e deep linking delle applicazioni
description: Questo articolo illustra come usare la funzionalità di indicizzazione e deep linking delle applicazioni per rendere disponibile per la ricerca il contenuto di applicazioni Xamarin.Forms in dispositivi iOS e Android.
ms.prod: xamarin
ms.assetid: 410C5D19-AA3C-4E0D-B799-E288C5803226
ms.technology: xamarin-forms
ms.custom: xamu-video
author: davidbritch
ms.author: dabritch
ms.date: 11/28/2018
ms.openlocfilehash: ca9c71d5bdac1900c4f0e5d07898e65b06cdcf90
ms.sourcegitcommit: b23a107b0fe3d2f814ae35b52a5855b6ce2a3513
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/20/2019
ms.locfileid: "65925567"
---
# <a name="application-indexing-and-deep-linking"></a>Indicizzazione e deep linking delle applicazioni

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://developer.xamarin.com/samples/xamarin-forms/DeepLinking/)

_L'indicizzazione delle applicazioni consente di mantenere rilevanti, visualizzandole nei risultati della ricerca, le applicazioni che verrebbero altrimenti dimenticate dopo pochi usi. Il deep linking consente alle applicazioni di rispondere a un risultato di ricerca che contiene dati dell'applicazione, in genere passando a una pagina a cui fa riferimento un collegamento diretto. Questo articolo illustra come usare indicizzazione e deep linking delle applicazioni per rendere disponibile per la ricerca il contenuto di applicazioni Xamarin.Forms in dispositivi iOS e Android._

> [!VIDEO https://youtube.com/embed/UJv4jUs7cJw]

**Deep Linking with Xamarin.Forms and Azure video** (Deep linking con Xamarin.Forms e Azure - video)


L'indicizzazione e il deep linking delle applicazioni Xamarin.Forms rendono disponibile un'API per pubblicare metadati per l'indicizzazione delle applicazioni man mano che gli utenti si spostano tra le applicazioni. Il contenuto indicizzato può quindi essere cercato nella ricerca Spotlight, in Google Search o in una ricerca Web. Quando si tocca un risultato di ricerca che contiene un collegamento diretto verrà generato un evento che può essere gestito da un'applicazione e viene generalmente usato per passare alla pagina a cui fa riferimento il collegamento diretto.

L'applicazione di esempio è una dimostrazione di un'applicazione di tipo Elenco attività, in cui i dati vengono archiviati in un database di SQLite locale, come illustrato negli screenshot seguenti:

![](deep-linking-images/screenshots.png "Applicazione TodoList")

Ogni istanza di `TodoItem` creata dall'utente viene indicizzata. È quindi possibile usare la funzionalità di ricerca specifica della piattaforma per individuare dati indicizzati dall'applicazione. Quando l'utente tocca un elemento nei risultati della ricerca per l'applicazione, l'applicazione viene avviata, si passa a `TodoItemPage` e viene visualizzato l'elemento `TodoItem` a cui fa riferimento il collegamento diretto.

Per altre informazioni sull'uso di un database SQLite, vedere [Database locali Xamarin.Forms](~/xamarin-forms/app-fundamentals/databases.md).

> [!NOTE]
> La funzionalità di indicizzazione e deep linking delle applicazioni Xamarin.Forms è disponibile solo per le piattaforme iOS e Android e richiede come minimo rispettivamente iOS 9 e API 23.

## <a name="setup"></a>Configurazione

Le sezioni seguenti includono eventuali istruzioni di configurazione aggiuntive per l'uso di questa funzionalità nelle piattaforme iOS e Android.

### <a name="ios"></a>iOS

Nella piattaforma iOS, assicurarsi che il progetto di piattaforma iOS imposti il file **entitlements.plist** come file di entitlement personalizzati per la firma del bundle.

Per usare i collegamenti universali iOS:

1. Aggiungere un entitlement Domini associati all'app, con la chiave `applinks`, includendo tutti i domini che saranno supportati dall'app.
1. Aggiungere un file di associazione del sito dell'app Apple nel sito Web.
1. Aggiungere la chiave `applinks` al file di associazione del sito dell'app Apple.

Per altre informazioni, vedere [Allowing Apps and Websites to Link to Your Content](https://developer.apple.com/documentation/uikit/core_app/allowing_apps_and_websites_to_link_to_your_content) (Consentire il collegamento a contenuto personalizzato da app e siti Web) su developer.apple.com.

### <a name="android"></a>Android

Nella piattaforma Android esistono vari prerequisiti che devono essere soddisfatti per l'uso della funzionalità di indicizzazione e deep linking delle applicazioni:

1. Una versione dell'applicazione deve essere pubblicata in Google Play.
1. Deve essere registrato un sito Web associato per l'applicazione nella console per sviluppatori di Google. Dopo aver associato l'applicazione a un sito Web, è possibile indicizzare gli URL che vengono usati sia per il sito Web che per l'applicazione, che possono quindi essere resi disponibili nei risultati della ricerca. Per altre informazioni, vedere [Indicizzazione delle app nella Ricerca Google](https://support.google.com/googleplay/android-developer/answer/6041489) nel sito Web di Google.
1. L'applicazione deve supportare gli Intent URL HTTP nella classe `MainActivity`, che indicano all'indicizzazione delle applicazioni i tipi di schemi di dati URL a cui può rispondere l'applicazione. Per altre informazioni, vedere [Configuring the Intent Filter](~/android/platform/app-linking.md#configure-intent-filter) (Configurazione del filtro Intent).

Quando questi prerequisiti sono soddisfatti, sono necessarie le operazioni di configurazione aggiuntive seguenti per usare l'indicizzazione e il deep linking delle applicazioni Xamarin.Forms nella piattaforma Android:

1. Installare il pacchetto NuGet [Xamarin.Forms.AppLinks](https://www.nuget.org/packages/Xamarin.Forms.AppLinks/) nel progetto dell'applicazione Android.
1. Nel file **MainActivity.cs** aggiungere una dichiarazione per usare lo spazio dei nomi `Xamarin.Forms.Platform.Android.AppLinks`.
1. Nel file **MainActivity.cs** aggiungere una dichiarazione per usare lo spazio dei nomi `Firebase`.
1. In un Web browser, creare un nuovo progetto tramite la [console Firebase](https://console.firebase.google.com/).
1. Nella console Firebase aggiungere Firebase all'app Android e immettere i dati richiesti.
1. Scaricare il file **google-services.json** risultante.
1. Aggiungere il file **google-services.json** nella directory radice del progetto Android e impostare **Azione compilazione** su **GoogleServicesJson**.
1. Nell'override `MainActivity.OnCreate` aggiungere la riga di codice seguente sotto `Forms.Init(this, bundle)`:

```csharp
FirebaseApp.InitializeApp(this);
AndroidAppLinks.Init(this);
```

Quando si aggiunge **google-services.json** al progetto (e si imposta l'azione di compilazione *GoogleServicesJson*\*), il processo di compilazione estrae l'ID client e la chiave API, quindi aggiunge queste credenziali al file manifesto generato.

Per altre informazioni, vedere [Deep Link Content with Xamarin.Forms URL Navigation](https://blog.xamarin.com/deep-link-content-with-xamarin-forms-url-navigation/) (Deep linking del contenuto con la navigazione URL di Xamarin.Forms) nel blog di Xamarin.

## <a name="indexing-a-page"></a>Indicizzazione di una pagina

Il processo per l'indicizzazione di una pagina e la relativa esposizione per la ricerca Google e Spotlight è il seguente:

1. Creare un elemento [`AppLinkEntry`](xref:Xamarin.Forms.AppLinkEntry) che contiene i metadati necessari per indicizzare la pagina, oltre a un collegamento diretto per tornare alla pagina quando l'utente seleziona il contenuto indicizzato nei risultati della ricerca.
1. Registrare l'istanza di [`AppLinkEntry`](xref:Xamarin.Forms.AppLinkEntry) per indicizzarla per la ricerca.

L'esempio di codice seguente illustra come creare un'istanza di [`AppLinkEntry`](xref:Xamarin.Forms.AppLinkEntry):

```csharp
AppLinkEntry GetAppLink(TodoItem item)
{
    var pageType = GetType().ToString();
    var pageLink = new AppLinkEntry
    {
        Title = item.Name,
        Description = item.Notes,
        AppLinkUri = new Uri($"http://{App.AppName}/{pageType}?id={item.ID}", UriKind.RelativeOrAbsolute),
        IsLinkActive = true,
        Thumbnail = ImageSource.FromFile("monkey.png")
    };

    pageLink.KeyValues.Add("contentType", "TodoItemPage");
    pageLink.KeyValues.Add("appName", App.AppName);
    pageLink.KeyValues.Add("companyName", "Xamarin");

    return pageLink;
}
```

L'istanza di [`AppLinkEntry`](xref:Xamarin.Forms.AppLinkEntry) contiene varie di proprietà i cui valori sono necessari per indicizzare la pagina e creare un collegamento diretto. Le proprietà [`Title`](xref:Xamarin.Forms.IAppLinkEntry.Title), [`Description`](xref:Xamarin.Forms.IAppLinkEntry.Description) e [`Thumbnail`](xref:Xamarin.Forms.IAppLinkEntry.Thumbnail) vengono usate per identificare il contenuto indicizzato quando viene visualizzato nei risultati della ricerca. La proprietà [`IsLinkActive`](xref:Xamarin.Forms.IAppLinkEntry.IsLinkActive) viene impostata su `true` per indicare che il contenuto indicizzato è attualmente visualizzato. La proprietà [`AppLinkUri`](xref:Xamarin.Forms.IAppLinkEntry.AppLinkUri) è un `Uri` che contiene le informazioni necessarie per tornare alla pagina corrente e visualizzare l'elemento `TodoItem` corrente. L'esempio seguente mostra un `Uri` di esempio per l'applicazione di esempio:

```csharp
http://deeplinking/DeepLinking.TodoItemPage?id=2
```

Questo `Uri` contiene tutte le informazioni necessarie per avviare l'app `deeplinking`, passare a `DeepLinking.TodoItemPage` e visualizzare l'elemento `TodoItem` con `ID` 2.

## <a name="registering-content-for-indexing"></a>Registrazione del contenuto per l'indicizzazione

Dopo aver creato un'istanza di [`AppLinkEntry`](xref:Xamarin.Forms.AppLinkEntry), è necessario registrarla per l'indicizzazione per fare in modo che compaia nei risultati della ricerca. Questa operazione viene eseguita con il metodo [`RegisterLink`](xref:Xamarin.Forms.IAppLinks.RegisterLink(Xamarin.Forms.IAppLinkEntry)), come illustrato nell'esempio di codice seguente:

```csharp
Application.Current.AppLinks.RegisterLink (appLink);
```

Questo codice aggiunge l'istanza di [`AppLinkEntry`](xref:Xamarin.Forms.AppLinkEntry) alla raccolta [`AppLinks`](xref:Xamarin.Forms.Application.AppLinks) dell'applicazione.

> [!NOTE]
> Il metodo `RegisterLink` può essere usato anche per aggiornare il contenuto che è stato indicizzato per una pagina.

Dopo la registrazione per l'indicizzazione, un'istanza di [`AppLinkEntry`](xref:Xamarin.Forms.AppLinkEntry) può essere visualizzata nei risultati della ricerca. Lo screenshot seguente mostra il contenuto indicizzato visualizzato nei risultati della ricerca nella piattaforma iOS:

![](deep-linking-images/ios-search.png "Contenuto indicizzato nei risultati della ricerca in iOS")

## <a name="de-registering-indexed-content"></a>Annullamento della registrazione di contenuto indicizzato

Il metodo [`DeregisterLink`](xref:Xamarin.Forms.IAppLinks.DeregisterLink(Xamarin.Forms.IAppLinkEntry)) viene usato per rimuovere il contenuto indicizzato dai risultati della ricerca, come illustrato nell'esempio di codice seguente:

```csharp
Application.Current.AppLinks.DeregisterLink (appLink);
```

Questo codice rimuove l'istanza di [`AppLinkEntry`](xref:Xamarin.Forms.AppLinkEntry) dalla raccolta [`AppLinks`](xref:Xamarin.Forms.Application.AppLinks) dell'applicazione.

> [!NOTE]
> In Android non è possibile rimuovere contenuto indicizzato dai risultati della ricerca.

<a name="responding" />

## <a name="responding-to-a-deep-link"></a>Risposta a un collegamento diretto

Quando il contenuto indicizzato viene visualizzato nei risultati della ricerca e viene selezionato da un utente, la classe `App` per l'applicazione riceverà una richiesta per gestire l'`Uri` contenuto nel contenuto indicizzato. Questa richiesta può essere elaborata nell'override [`OnAppLinkRequestReceived`](xref:Xamarin.Forms.Application.OnAppLinkRequestReceived(System.Uri)), come illustrato nell'esempio di codice seguente:

```csharp
public class App : Application
{
    ...
    protected override async void OnAppLinkRequestReceived(Uri uri)
    {
        string appDomain = "http://" + App.AppName.ToLowerInvariant() + "/";
        if (!uri.ToString().ToLowerInvariant().StartsWith(appDomain, StringComparison.Ordinal))
            return;

        string pageUrl = uri.ToString().Replace(appDomain, string.Empty).Trim();
        var parts = pageUrl.Split('?');
        string page = parts[0];
        string pageParameter = parts[1].Replace("id=", string.Empty);

        var formsPage = Activator.CreateInstance(Type.GetType(page));
        var todoItemPage = formsPage as TodoItemPage;
        if (todoItemPage != null)
        {
            var todoItem = await App.Database.GetItemAsync(int.Parse(pageParameter));
            todoItemPage.BindingContext = todoItem;
            await MainPage.Navigation.PushAsync(formsPage as Page);
        }
        base.OnAppLinkRequestReceived(uri);
    }
}
```

Il metodo [`OnAppLinkRequestReceived`](xref:Xamarin.Forms.Application.OnAppLinkRequestReceived(System.Uri)) controlla che l'`Uri` ricevuto sia destinato all'applicazione, prima di analizzare l'`Uri` nella pagina di destinazione dello spostamento e il parametro da passare alla pagina. Viene creata un'istanza della pagina di destinazione e viene recuperato l'elemento `TodoItem` rappresentato dal parametro della pagina. Il [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) della pagina di destinazione viene quindi impostato sull'elemento `TodoItem`. Ciò garantisce che, quando la `TodoItemPage` viene visualizzata dal metodo [`PushAsync`](xref:Xamarin.Forms.INavigation.PushAsync(Xamarin.Forms.Page)), visualizzi l'elemento `TodoItem` il cui `ID` è contenuto nel collegamento diretto.

## <a name="making-content-available-for-search-indexing"></a>Rendere il contenuto disponibile per l'indicizzazione per la ricerca

Ogni volta che la pagina rappresentata da un collegamento diretto viene visualizzata, la proprietà [`AppLinkEntry.IsLinkActive`](xref:Xamarin.Forms.IAppLinkEntry.IsLinkActive) può essere impostata su `true`. In iOS e Android, in questo modo l'istanza di [`AppLinkEntry`](xref:Xamarin.Forms.AppLinkEntry) viene resa disponibile per l'indicizzazione per ricerca e, solo in iOS, l'istanza di `AppLinkEntry` diventa anche disponibile per Handoff. Per altre informazioni su Handoff, vedere [Introduction to Handoff](~/ios/platform/handoff.md) (Introduzione a Handoff).

L'esempio di codice seguente illustra l'impostazione della proprietà [`AppLinkEntry.IsLinkActive`](xref:Xamarin.Forms.IAppLinkEntry.IsLinkActive) su `true` nell'override [`Page.OnAppearing`](xref:Xamarin.Forms.Page.OnAppearing):

```csharp
protected override void OnAppearing()
{
    appLink = GetAppLink(BindingContext as TodoItem);
    if (appLink != null)
    {
        appLink.IsLinkActive = true;
    }
}
```

In modo analogo, quando si esce dalla pagina rappresentata da un collegamento diretto, la proprietà [`AppLinkEntry.IsLinkActive`](xref:Xamarin.Forms.IAppLinkEntry.IsLinkActive) può essere impostata su `false`. In iOS e Android, in questo modo l'istanza di [`AppLinkEntry`](xref:Xamarin.Forms.AppLinkEntry) non viene più annunciata per l'indicizzazione per la ricerca e, solo in iOS, l'istanza di `AppLinkEntry` non viene inoltre più annunciata per Handoff. Questa operazione può essere eseguita nell'override [`Page.OnDisappearing`](xref:Xamarin.Forms.Page.OnDisappearing), come illustrato nell'esempio di codice seguente:

```csharp
protected override void OnDisappearing()
{
    if (appLink != null)
    {
        appLink.IsLinkActive = false;
    }
}
```

## <a name="providing-data-to-handoff"></a>Trasferimento di dati a Handoff

In iOS, i dati specifici dell'applicazione possono essere archiviati durante l'indicizzazione della pagina. Questo risultato viene ottenuto aggiungendo i dati alla raccolta [`KeyValues`](xref:Xamarin.Forms.IAppLinkEntry.KeyValues), ovvero un `Dictionary<string, string>` per l'archiviazione di coppie chiave-valore usate in Handoff. Handoff è una funzionalità che consente all'utente di avviare un'attività in uno dei propri dispositivi e continuare tale attività in un'altra dei dispositivi (identificato dall'account iCloud dell'utente). Il codice seguente mostra un esempio di archiviazione di coppie chiave-valore specifiche dell'applicazione:

```csharp
var pageLink = new AppLinkEntry
{
    ...
};
pageLink.KeyValues.Add("appName", App.AppName);
pageLink.KeyValues.Add("companyName", "Xamarin");
```

I valori archiviati nella raccolta [`KeyValues`](xref:Xamarin.Forms.IAppLinkEntry.KeyValues) verranno archiviati nei metadati per la pagina indicizzata e verranno ripristinati quando l'utente tocca un risultato della ricerca che contiene un collegamento diretto (o quando si usa Handoff per visualizzare il contenuto in un dispositivo connesso).

È anche possibile specificare valori per le chiavi seguenti:

- `contentType` - Valore `string` che specifica l'identificatore di tipo uniforme del contenuto indicizzato. La convenzione consigliata da usare per questo valore è il nome di tipo della pagina contenente il contenuto indicizzato.
- `associatedWebPage` - Valore `string` che rappresenta la pagina Web da visitare se anche il contenuto indicizzato può essere visualizzato nel Web o se l'applicazione supporta i collegamenti diretti di Safari.
- `shouldAddToPublicIndex` - Valore `string` `true` o `false` che determina se aggiungere o meno il contenuto indicizzato all'indice del cloud pubblico di Apple, che può quindi essere presentato agli utenti che non hanno installato l'applicazione nel proprio dispositivo iOS. La semplice impostazione del contenuto per l'indicizzazione pubblica, tuttavia, non significa che verrà aggiunto automaticamente all'indice del cloud pubblico di Apple. Per altre informazioni, vedere [Public Search Indexing](~/ios/platform/search/nsuseractivity.md) (Indicizzazione per la ricerca pubblica). Si noti che questa chiave deve essere impostata su `false` quando si aggiungono dati personali alla raccolta [`KeyValues`](xref:Xamarin.Forms.IAppLinkEntry.KeyValues).

> [!NOTE]
> La raccolta `KeyValues` non viene usata nella piattaforma Android.

Per altre informazioni su Handoff, vedere [Introduction to Handoff](~/ios/platform/handoff.md) (Introduzione a Handoff).

## <a name="summary"></a>Riepilogo

In questo articolo è stato illustrato come usare indicizzazione e deep linking delle applicazioni per rendere disponibile per la ricerca il contenuto di applicazioni Xamarin.Forms in dispositivi iOS e Android. L'indicizzazione delle applicazioni consente di mantenere rilevanti, visualizzandole nei risultati della ricerca, le applicazioni che verrebbero altrimenti dimenticate dopo pochi usi.

## <a name="related-links"></a>Collegamenti correlati

- [Deep Linking (sample)](https://developer.xamarin.com/samples/xamarin-forms/DeepLinking/) (Esempio di deep linking)
- [iOS Search APIs](~/ios/platform/search/index.md) (API per la ricerca iOS)
- [App-Linking in Android 6.0](~/android/platform/app-linking.md)
- [AppLinkEntry](xref:Xamarin.Forms.AppLinkEntry)
- [IAppLinkEntry](xref:Xamarin.Forms.IAppLinkEntry)
- [IAppLinks](xref:Xamarin.Forms.IAppLinks)
