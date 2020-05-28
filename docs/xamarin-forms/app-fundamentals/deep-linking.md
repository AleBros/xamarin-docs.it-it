---
title: ''
description: Questo articolo illustra come usare l'indicizzazione e la deep linking delle applicazioni per rendere Xamarin.Forms ricercabile il contenuto dell'applicazione nei dispositivi iOS e Android.
ms.prod: ''
ms.assetid: ''
ms.technology: ''
ms.custom: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: d93f9bbcfafc3cb71d6b71159f6d3368f50c08be
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/28/2020
ms.locfileid: "84135733"
---
# <a name="application-indexing-and-deep-linking"></a>Indicizzazione e deep linking delle applicazioni

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/deeplinking)

_L'indicizzazione dell'applicazione consente alle applicazioni che altrimenti verrebbero dimenticate di rimanere pertinenti nei risultati della ricerca. Il Deep linking consente alle applicazioni di rispondere a un risultato della ricerca che contiene i dati dell'applicazione, in genere passando a una pagina a cui viene fatto riferimento da un collegamento profondo. Questo articolo illustra come usare l'indicizzazione e la deep linking delle applicazioni per rendere Xamarin.Forms ricercabile il contenuto dell'applicazione nei dispositivi iOS e Android._

> [!VIDEO https://youtube.com/embed/UJv4jUs7cJw]

**Deep linking con Xamarin.Forms e video di Azure**

Xamarin.Formsl'indicizzazione e la deep linking delle applicazioni forniscono un'API per la pubblicazione di metadati per l'indicizzazione dell'applicazione mentre gli utenti navigano nelle applicazioni Il contenuto indicizzato può quindi essere cercato nella ricerca Spotlight, in Google Search o in una ricerca Web. Quando si tocca un risultato di ricerca che contiene un collegamento diretto verrà generato un evento che può essere gestito da un'applicazione e viene generalmente usato per passare alla pagina a cui fa riferimento il collegamento diretto.

L'applicazione di esempio è una dimostrazione di un'applicazione di tipo Elenco attività, in cui i dati vengono archiviati in un database di SQLite locale, come illustrato negli screenshot seguenti:

![](deep-linking-images/screenshots.png "TodoList Application")

Ogni istanza di `TodoItem` creata dall'utente viene indicizzata. È quindi possibile usare la funzionalità di ricerca specifica della piattaforma per individuare dati indicizzati dall'applicazione. Quando l'utente tocca un elemento nei risultati della ricerca per l'applicazione, l'applicazione viene avviata, si passa a `TodoItemPage` e viene visualizzato l'elemento `TodoItem` a cui fa riferimento il collegamento diretto.

Per ulteriori informazioni sull'utilizzo di un database SQLite, vedere [ Xamarin.Forms database locali](~/xamarin-forms/data-cloud/data/databases.md).

> [!NOTE]
> Xamarin.Formsle funzionalità di indicizzazione e deep linking delle applicazioni sono disponibili solo nelle piattaforme iOS e Android e richiedono rispettivamente almeno iOS 9 e l'API 23.

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

Una volta soddisfatti questi prerequisiti, è necessaria la seguente installazione aggiuntiva per usare Xamarin.Forms l'indicizzazione e la Deep linking delle applicazioni nella piattaforma Android:

1. Installare [ Xamarin.Forms . AppLinks](https://www.nuget.org/packages/Xamarin.Forms.AppLinks/) il pacchetto NuGet nel progetto di applicazione Android.
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

> [!NOTE]
> In questo articolo i termini collegamenti alle applicazioni e collegamenti diretti vengono spesso usati in modo intercambiabile. Tuttavia, in Android questi termini hanno significati distinti. In Android un collegamento diretto è un filtro Intent che consente agli utenti di accedere direttamente a un'attività specifica nell'app. Se si fa clic su un collegamento diretto è possibile che si apra una finestra di dialogo di disambiguazione, che consente all'utente di selezionare una delle app in grado di gestire l'URL. Un collegamento all'app Android è un collegamento diretto basato sull'URL del sito Web, che è stato verificato per l'appartenenza al sito Web. Se si fa clic su un collegamento all'app, se installata l'app si apre senza che venga aperta una finestra di dialogo di disambiguazione.

Per altre informazioni, vedere [deep link content with Xamarin.Forms URL Navigation](https://blog.xamarin.com/deep-link-content-with-xamarin-forms-url-navigation/) nel Blog di Novell.

## <a name="indexing-a-page"></a>Indicizzazione di una pagina

Il processo per l'indicizzazione di una pagina e la relativa esposizione per la ricerca Google e Spotlight è il seguente:

1. Creare un oggetto [`AppLinkEntry`](xref:Xamarin.Forms.AppLinkEntry) che contiene i metadati necessari per indicizzare la pagina, insieme a un collegamento profondo per tornare alla pagina quando l'utente seleziona il contenuto indicizzato nei risultati della ricerca.
1. Registrare l' [`AppLinkEntry`](xref:Xamarin.Forms.AppLinkEntry) istanza di per indicizzarla per la ricerca.

Nell'esempio di codice riportato di seguito viene illustrato come creare un' [`AppLinkEntry`](xref:Xamarin.Forms.AppLinkEntry) istanza di:

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

L' [`AppLinkEntry`](xref:Xamarin.Forms.AppLinkEntry) istanza contiene un numero di proprietà i cui valori sono necessari per indicizzare la pagina e creare un collegamento profondo. Le [`Title`](xref:Xamarin.Forms.IAppLinkEntry.Title) [`Description`](xref:Xamarin.Forms.IAppLinkEntry.Description) proprietà, e [`Thumbnail`](xref:Xamarin.Forms.IAppLinkEntry.Thumbnail) vengono usate per identificare il contenuto indicizzato quando viene visualizzato nei risultati della ricerca. La [`IsLinkActive`](xref:Xamarin.Forms.IAppLinkEntry.IsLinkActive) proprietà è impostata su `true` per indicare che il contenuto indicizzato è attualmente in fase di visualizzazione. La [`AppLinkUri`](xref:Xamarin.Forms.IAppLinkEntry.AppLinkUri) proprietà è un oggetto `Uri` che contiene le informazioni necessarie per tornare alla pagina corrente e visualizzare l'oggetto corrente `TodoItem` . L'esempio seguente mostra un `Uri` di esempio per l'applicazione di esempio:

```csharp
http://deeplinking/DeepLinking.TodoItemPage?id=2
```

Questo `Uri` contiene tutte le informazioni necessarie per avviare l'app `deeplinking`, passare a `DeepLinking.TodoItemPage` e visualizzare l'elemento `TodoItem` con `ID` 2.

## <a name="registering-content-for-indexing"></a>Registrazione del contenuto per l'indicizzazione

Una volta [`AppLinkEntry`](xref:Xamarin.Forms.AppLinkEntry) creata un'istanza, è necessario registrarla affinché l'indicizzazione venga visualizzata nei risultati della ricerca. Questa operazione viene eseguita con [ `RegisterLink` ] (xrif: Xamarin.Forms . IAppLinks. RegisterLink ( Xamarin.Forms . IAppLinkEntry)), come illustrato nell'esempio di codice seguente:

```csharp
Application.Current.AppLinks.RegisterLink (appLink);
```

Questa operazione aggiunge l' [`AppLinkEntry`](xref:Xamarin.Forms.AppLinkEntry) istanza alla raccolta dell' [`AppLinks`](xref:Xamarin.Forms.Application.AppLinks) applicazione.

> [!NOTE]
> Il metodo `RegisterLink` può essere usato anche per aggiornare il contenuto che è stato indicizzato per una pagina.

Una volta che un' [`AppLinkEntry`](xref:Xamarin.Forms.AppLinkEntry) istanza è stata registrata per l'indicizzazione, può essere visualizzata nei risultati della ricerca. Lo screenshot seguente mostra il contenuto indicizzato visualizzato nei risultati della ricerca nella piattaforma iOS:

![](deep-linking-images/ios-search.png "Indexed Content in Search Results on iOS")

## <a name="de-registering-indexed-content"></a>Annullamento della registrazione di contenuto indicizzato

[ `DeregisterLink` ] (Xrif: Xamarin.Forms . IAppLinks. DeregisterLink ( Xamarin.Forms . IAppLinkEntry)) il metodo viene usato per rimuovere il contenuto indicizzato dai risultati della ricerca, come illustrato nell'esempio di codice seguente:

```csharp
Application.Current.AppLinks.DeregisterLink (appLink);
```

In questo modo l' [`AppLinkEntry`](xref:Xamarin.Forms.AppLinkEntry) istanza viene rimossa dalla raccolta dell'applicazione [`AppLinks`](xref:Xamarin.Forms.Application.AppLinks) .

> [!NOTE]
> In Android non è possibile rimuovere contenuto indicizzato dai risultati della ricerca.

<a name="responding" />

## <a name="responding-to-a-deep-link"></a>Risposta a un collegamento diretto

Quando il contenuto indicizzato viene visualizzato nei risultati della ricerca e viene selezionato da un utente, la classe `App` per l'applicazione riceverà una richiesta per gestire l'`Uri` contenuto nel contenuto indicizzato. Questa richiesta può essere elaborata nell' [`OnAppLinkRequestReceived`](xref:Xamarin.Forms.Application.OnAppLinkRequestReceived(System.Uri)) override, come illustrato nell'esempio di codice seguente:

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

Il [`OnAppLinkRequestReceived`](xref:Xamarin.Forms.Application.OnAppLinkRequestReceived(System.Uri)) metodo verifica che l'oggetto ricevuto `Uri` sia destinato all'applicazione, prima di eseguire l'analisi dell'oggetto `Uri` nella pagina in cui spostarsi e il parametro da passare alla pagina. Viene creata un'istanza della pagina di destinazione e viene recuperato l'elemento `TodoItem` rappresentato dal parametro della pagina. La [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) della pagina da esplorare viene quindi impostata su `TodoItem` . In questo modo, quando l'oggetto `TodoItemPage` viene visualizzato da [ `PushAsync` ] (xrif: Xamarin.Forms . INavigation. PushAsync ( Xamarin.Forms . Page)), verrà visualizzato l'oggetto il `TodoItem` cui `ID` è contenuto nel collegamento Deep.

## <a name="making-content-available-for-search-indexing"></a>Rendere il contenuto disponibile per l'indicizzazione per la ricerca

Ogni volta che viene visualizzata la pagina rappresentata da un collegamento approfondito, la [`AppLinkEntry.IsLinkActive`](xref:Xamarin.Forms.IAppLinkEntry.IsLinkActive) proprietà può essere impostata su `true` . In iOS e Android rende l' [`AppLinkEntry`](xref:Xamarin.Forms.AppLinkEntry) istanza disponibile per l'indicizzazione della ricerca e solo in iOS rende anche disponibile l' `AppLinkEntry` istanza per la consegna. Per altre informazioni su Handoff, vedere [Introduction to Handoff](~/ios/platform/handoff.md) (Introduzione a Handoff).

Nell'esempio di codice riportato di seguito viene illustrata l'impostazione della [`AppLinkEntry.IsLinkActive`](xref:Xamarin.Forms.IAppLinkEntry.IsLinkActive) proprietà su `true` nell' [`Page.OnAppearing`](xref:Xamarin.Forms.Page.OnAppearing) override:

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

Analogamente, quando la pagina rappresentata da un collegamento profondo viene spostata da, la [`AppLinkEntry.IsLinkActive`](xref:Xamarin.Forms.IAppLinkEntry.IsLinkActive) proprietà può essere impostata su `false` . In iOS e Android, in questo [`AppLinkEntry`](xref:Xamarin.Forms.AppLinkEntry) caso l'istanza viene annunciata per l'indicizzazione della ricerca e solo in iOS, ma interrompe anche l'annuncio dell' `AppLinkEntry` istanza per la consegna. Questa operazione può essere eseguita nell' [`Page.OnDisappearing`](xref:Xamarin.Forms.Page.OnDisappearing) override, come illustrato nell'esempio di codice seguente:

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

In iOS, i dati specifici dell'applicazione possono essere archiviati durante l'indicizzazione della pagina. Questa operazione viene eseguita aggiungendo i dati alla [`KeyValues`](xref:Xamarin.Forms.IAppLinkEntry.KeyValues) raccolta, che è un `Dictionary<string, string>` per archiviare le coppie chiave-valore usate in una consegna. Handoff è una funzionalità che consente all'utente di avviare un'attività in uno dei propri dispositivi e continuare tale attività in un'altra dei dispositivi (identificato dall'account iCloud dell'utente). Il codice seguente mostra un esempio di archiviazione di coppie chiave-valore specifiche dell'applicazione:

```csharp
var pageLink = new AppLinkEntry
{
    ...
};
pageLink.KeyValues.Add("appName", App.AppName);
pageLink.KeyValues.Add("companyName", "Xamarin");
```

I valori archiviati nella [`KeyValues`](xref:Xamarin.Forms.IAppLinkEntry.KeyValues) raccolta verranno archiviati nei metadati per la pagina indicizzata e verranno ripristinati quando l'utente tocca un risultato della ricerca che contiene un collegamento profondo (o quando viene usata la consegna per visualizzare il contenuto in un altro dispositivo connesso).

È anche possibile specificare valori per le chiavi seguenti:

- `contentType` - Valore `string` che specifica l'identificatore di tipo uniforme del contenuto indicizzato. La convenzione consigliata da usare per questo valore è il nome di tipo della pagina contenente il contenuto indicizzato.
- `associatedWebPage` - Valore `string` che rappresenta la pagina Web da visitare se anche il contenuto indicizzato può essere visualizzato nel Web o se l'applicazione supporta i collegamenti diretti di Safari.
- `shouldAddToPublicIndex` - Valore `string``true` o `false` che determina se aggiungere o meno il contenuto indicizzato all'indice del cloud pubblico di Apple, che può quindi essere presentato agli utenti che non hanno installato l'applicazione nel proprio dispositivo iOS. La semplice impostazione del contenuto per l'indicizzazione pubblica, tuttavia, non significa che verrà aggiunto automaticamente all'indice del cloud pubblico di Apple. Per altre informazioni, vedere [Public Search Indexing](~/ios/platform/search/nsuseractivity.md) (Indicizzazione per la ricerca pubblica). Si noti che questa chiave deve essere impostata su `false` quando si aggiungono dati personali alla [`KeyValues`](xref:Xamarin.Forms.IAppLinkEntry.KeyValues) raccolta.

> [!NOTE]
> La raccolta `KeyValues` non viene usata nella piattaforma Android.

Per altre informazioni su Handoff, vedere [Introduction to Handoff](~/ios/platform/handoff.md) (Introduzione a Handoff).

## <a name="summary"></a>Riepilogo

Questo articolo ha illustrato come usare l'indicizzazione e la deep linking delle applicazioni per rendere le applicazioni disponibili per la Xamarin.Forms ricerca nei dispositivi iOS e Android. L'indicizzazione delle applicazioni consente di mantenere rilevanti, visualizzandole nei risultati della ricerca, le applicazioni che verrebbero altrimenti dimenticate dopo pochi usi.

## <a name="related-links"></a>Collegamenti correlati

- [Deep Linking (sample)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/deeplinking) (Esempio di deep linking)
- [iOS Search APIs](~/ios/platform/search/index.md) (API per la ricerca iOS)
- [App-Linking in Android 6.0](~/android/platform/app-linking.md)
- [AppLinkEntry](xref:Xamarin.Forms.AppLinkEntry)
- [IAppLinkEntry](xref:Xamarin.Forms.IAppLinkEntry)
- [IAppLinks](xref:Xamarin.Forms.IAppLinks)
