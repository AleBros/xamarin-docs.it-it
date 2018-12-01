---
title: Applicazione di indicizzazione e Deep Linking
description: Questo articolo illustra come usare l'applicazione di indicizzazione e deep linking per rendere disponibili per la ricerca in dispositivi iOS e Android il contenuto dell'applicazione xamarin. Forms.
ms.prod: xamarin
ms.assetid: 410C5D19-AA3C-4E0D-B799-E288C5803226
ms.technology: xamarin-forms
ms.custom: xamu-video
author: davidbritch
ms.author: dabritch
ms.date: 11/28/2018
ms.openlocfilehash: f73760e2dc2310a9c1cd7a63a03ead37283a415f
ms.sourcegitcommit: 215cad17324ba3fbc23487ce66cd4e1cc74eb879
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/30/2018
ms.locfileid: "52710010"
---
# <a name="application-indexing-and-deep-linking"></a>Applicazione di indicizzazione e Deep Linking

_L'indicizzazione dell'applicazione consente alle applicazioni in caso contrario, può essere dimenticate dopo poche Usa per rimanere al passo dalla visualizzazione nei risultati della ricerca. Il collegamento completo consente alle applicazioni rispondere a un risultato di ricerca che contiene i dati dell'applicazione, in genere passando a una pagina di cui viene fatto riferimento da un collegamento diretto. Questo articolo illustra come usare l'applicazione di indicizzazione e deep linking per rendere disponibili per la ricerca in dispositivi iOS e Android il contenuto dell'applicazione xamarin. Forms._

> [!VIDEO https://youtube.com/embed/UJv4jUs7cJw]

**Collegamento profonde con xamarin. Forms e Azure, da [Xamarin University](https://university.xamarin.com/)**


L'indicizzazione dell'applicazione xamarin. Forms e deep linking forniscono un'API per pubblicare metadati per l'applicazione indicizzazione man mano che gli utenti si spostano le applicazioni. Contenuto indicizzato può essere cercato quindi nella ricerca Spotlight, in ricerca di Google o in una ricerca sul web. Se si tocca un risultato di ricerca che contiene un collegamento diretto genererà un evento che può essere gestito da un'applicazione e viene generalmente utilizzato per passare alla pagina a cui fa riferimento il collegamento diretto.

L'applicazione di esempio viene illustrata un'applicazione di elenco Todo in cui i dati vengono archiviati in un database SQLite locale, come illustrato negli screenshot seguenti:

![](deep-linking-images/screenshots.png "Applicazione di TodoList")

Ogni `TodoItem` istanza creata dall'utente viene indicizzato. Ricerca specifica della piattaforma è quindi utilizzabile per individuare dati indicizzati dall'applicazione. Quando l'utente tocca un elemento del risultato di ricerca per l'applicazione, l'applicazione viene avviata, il `TodoItemPage` si passa a e il `TodoItem` a cui fa riferimento il deep collegamento viene visualizzato.

Per altre informazioni sull'uso di un database SQLite, vedere [database locali di xamarin. Forms](~/xamarin-forms/app-fundamentals/databases.md).

> [!NOTE]
> L'indicizzazione dell'applicazione xamarin. Forms e l'avanzato di funzionalità di collegamento è disponibile solo per le piattaforme iOS e Android e richiede un minimo di iOS 9 e API 23, rispettivamente.

## <a name="setup"></a>Configurazione

Le sezioni seguenti forniscono le istruzioni di configurazione aggiuntivi per l'uso di questa funzionalità nelle piattaforme Android e iOS.

### <a name="ios"></a>iOS

Nella piattaforma iOS, assicurarsi che il progetto di piattaforma iOS imposta il **entitlements. plist** come il file di Entitlement personalizzati per la firma del bundle.

Per usare i collegamenti universali iOS:

1. Aggiungere un diritto di domini associati all'App, con la `applinks` principali, inclusi tutti i domini di verranno supportate dall'app.
1. Aggiungere un file di associazione di Apple App sito nel sito Web.
1. Aggiungere il `applinks` chiave nel file di associazione di Apple App sito.

Per altre informazioni, vedere [consentendo di App e siti Web a Link to Your Content](https://developer.apple.com/documentation/uikit/core_app/allowing_apps_and_websites_to_link_to_your_content) su developer.apple.com.

### <a name="android"></a>Android

La piattaforma Android, esistono alcuni prerequisiti che devono essere soddisfatti per l'uso dell'applicazione l'indicizzazione e deep funzionalità di collegamento:

1. Una versione dell'applicazione deve essere in tempo reale in Google Play.
1. Un sito Web complementare deve essere registrato nei confronti dell'applicazione nella Console per sviluppatori di Google. Dopo l'applicazione è associata a un sito Web, gli URL possono essere indicizzati che funzionano per il sito Web e l'applicazione, che quindi può essere reso disponibile nei risultati della ricerca. Per altre informazioni, vedere [App di indicizzazione in ricerca di Google](https://support.google.com/googleplay/android-developer/answer/6041489) sul sito Web di Google.
1. L'applicazione deve supportare gli Intent URL HTTP nella `MainActivity` (classe), che obbliga indicizzazione quali tipi di schemi di dati di URL dell'applicazione l'applicazione può rispondere. Per altre informazioni, vedere [configurazione del filtro Intent](~/android/platform/app-linking.md#configure-intent-filter).

Una volta soddisfatti questi prerequisiti, per utilizzare l'indicizzazione dell'applicazione xamarin. Forms e deep linking nella piattaforma Android è necessario il programma di installazione aggiuntivo seguente:

1. Installare il [Xamarin.Forms.AppLinks](https://www.nuget.org/packages/Xamarin.Forms.AppLinks/) pacchetto NuGet nel progetto di applicazione Android.
1. Nel **MainActivity.cs** , aggiungere una dichiarazione di usare il `Xamarin.Forms.Platform.Android.AppLinks` dello spazio dei nomi.
1. Nel **MainActivity.cs** , aggiungere una dichiarazione di usare il `Firebase` dello spazio dei nomi.
1. In un web browser, creare un nuovo progetto tramite il [Console di Firebase](https://console.firebase.google.com/).
1. Nella Console di Firebase, aggiungere Firebase all'App Android e immettere i dati necessari.
1. Scaricare l'oggetto risultante **google-Services. JSON** file.
1. Aggiungere il **google-Services. JSON** file nella directory radice del progetto Android e impostare relativo **azione di compilazione** al **GoogleServicesJson**.
1. Nel `MainActivity.OnCreate` eseguire l'override, aggiungere la seguente riga di codice sotto `Forms.Init(this, bundle)`:

```csharp
FirebaseApp.InitializeApp(this);
AndroidAppLinks.Init(this);
```

Quando **google-Services. JSON** viene aggiunto al progetto (e il *GoogleServicesJson** è impostato l'azione di compilazione), il processo di compilazione estrae la chiave API e l'ID client e quindi aggiunge queste credenziali per il file manifesto generato.

Per altre informazioni, vedere [Deep Link contenuto con navigazione URL con xamarin. Forms](https://blog.xamarin.com/deep-link-content-with-xamarin-forms-url-navigation/) sul blog di Xamarin.

## <a name="indexing-a-page"></a>Indicizzazione di una pagina

Il processo per l'indicizzazione di una pagina e l'esposizione a ricerca di Spotlight e Google è come segue:

1. Creare un [ `AppLinkEntry` ](xref:Xamarin.Forms.AppLinkEntry) che contiene i metadati necessari per indicizzare la pagina, oltre a un collegamento diretto per tornare alla pagina quando l'utente seleziona il contenuto indicizzato nei risultati della ricerca.
1. Registrare il [ `AppLinkEntry` ](xref:Xamarin.Forms.AppLinkEntry) istanza per indicizzarla, per la ricerca.

Esempio di codice seguente viene illustrato come creare un [ `AppLinkEntry` ](xref:Xamarin.Forms.AppLinkEntry) istanza:

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

Il [ `AppLinkEntry` ](xref:Xamarin.Forms.AppLinkEntry) istanza contiene un numero di proprietà i cui valori sono necessari per la pagina di indice e creare un collegamento diretto. Il [ `Title` ](xref:Xamarin.Forms.IAppLinkEntry.Title), [ `Description` ](xref:Xamarin.Forms.IAppLinkEntry.Description), e [ `Thumbnail` ](xref:Xamarin.Forms.IAppLinkEntry.Thumbnail) proprietà vengono usate per identificare il contenuto indicizzato quando viene visualizzato nei risultati della ricerca. Il [ `IsLinkActive` ](xref:Xamarin.Forms.IAppLinkEntry.IsLinkActive) è impostata su `true` per indicare che il contenuto indicizzato è attualmente visualizzato. Il [ `AppLinkUri` ](xref:Xamarin.Forms.IAppLinkEntry.AppLinkUri) proprietà è un `Uri` che contiene le informazioni necessarie per tornare alla pagina corrente e di visualizzazione corrente `TodoItem`. L'esempio seguente illustra un esempio `Uri` per l'applicazione di esempio:

```csharp
http://deeplinking/DeepLinking.TodoItemPage?id=2
```

Ciò `Uri` contiene tutte le informazioni necessarie per avviare il `deeplinking` app, passare al `DeepLinking.TodoItemPage`e visualizzare il `TodoItem` che ha un `ID` pari a 2.

## <a name="registering-content-for-indexing"></a>La registrazione di contenuto per l'indicizzazione

Una volta un' [ `AppLinkEntry` ](xref:Xamarin.Forms.AppLinkEntry) istanza è stata creata, è necessario registrarlo per l'indicizzazione deve essere visualizzato nei risultati della ricerca. Questa operazione viene eseguita con il [ `RegisterLink` ](xref:Xamarin.Forms.IAppLinks.RegisterLink(Xamarin.Forms.IAppLinkEntry)) metodo, come illustrato nell'esempio di codice seguente:

```csharp
Application.Current.AppLinks.RegisterLink (appLink);
```

Ciò consente di aggiungere il [ `AppLinkEntry` ](xref:Xamarin.Forms.AppLinkEntry) istanza dell'applicazione [ `AppLinks` ](xref:Xamarin.Forms.Application.AppLinks) raccolta.

> [!NOTE]
> Il `RegisterLink` metodo può essere utilizzato anche per aggiornare il contenuto che è stato indicizzato per una pagina.

Una volta un' [ `AppLinkEntry` ](xref:Xamarin.Forms.AppLinkEntry) istanza è stata registrata per l'indicizzazione, può essere visualizzato nei risultati della ricerca. Lo screenshot seguente mostra il contenuto indicizzato visualizzazione nei risultati della ricerca nella piattaforma iOS:

![](deep-linking-images/ios-search.png "Contenuto indicizzato nei risultati della ricerca in iOS")

## <a name="de-registering-indexed-content"></a>Annullando la registrazione di indicizzazione del contenuto

Il [ `DeregisterLink` ](xref:Xamarin.Forms.IAppLinks.DeregisterLink(Xamarin.Forms.IAppLinkEntry)) metodo viene utilizzato per rimuovere il contenuto indicizzato dai risultati della ricerca, come illustrato nell'esempio di codice seguente:

```csharp
Application.Current.AppLinks.DeregisterLink (appLink);
```

Questa operazione rimuove la [ `AppLinkEntry` ](xref:Xamarin.Forms.AppLinkEntry) istanza dell'applicazione [ `AppLinks` ](xref:Xamarin.Forms.Application.AppLinks) raccolta.

> [!NOTE]
> In Android non è possibile rimuovere contenuto indicizzato dai risultati della ricerca.

<a name="responding" />

## <a name="responding-to-a-deep-link"></a>Risponde a un collegamento diretto

Quando il contenuto indicizzato viene visualizzato nei risultati della ricerca e viene selezionato da un utente, il `App` classe per l'applicazione riceverà una richiesta per gestire il `Uri` contenuti nel contenuto indicizzato. Questa richiesta può essere elaborata nel [ `OnAppLinkRequestReceived` ](xref:Xamarin.Forms.Application.OnAppLinkRequestReceived(System.Uri)) sostituire, come illustrato nell'esempio di codice seguente:

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

Il [ `OnAppLinkRequestReceived` ](xref:Xamarin.Forms.Application.OnAppLinkRequestReceived(System.Uri)) metodo controlla che i dati ricevuti `Uri` è destinata l'applicazione, prima dell'analisi di `Uri` nella pagina per spostarsi a e il parametro da passare alla pagina. Un'istanza della pagina per spostarsi a viene creato e `TodoItem` rappresentato dalla pagina parametro viene recuperato. Il [ `BindingContext` ](xref:Xamarin.Forms.BindableObject.BindingContext) della pagina per spostarsi a viene quindi impostato sul `TodoItem`. Ciò garantisce che, quando il `TodoItemPage` viene visualizzato per il [ `PushAsync` ](xref:Xamarin.Forms.INavigation.PushAsync(Xamarin.Forms.Page)) (metodo), verrà illustrati il `TodoItem` cui `ID` è contenuto nel collegamento diretto.

## <a name="making-content-available-for-search-indexing"></a>Rendendo disponibili per l'indicizzazione della ricerca contenuto

Ogni volta che viene visualizzata la pagina rappresentata da un collegamento diretto, il [ `AppLinkEntry.IsLinkActive` ](xref:Xamarin.Forms.IAppLinkEntry.IsLinkActive) può essere impostata su `true`. In iOS e Android in questo modo il [ `AppLinkEntry` ](xref:Xamarin.Forms.AppLinkEntry) istanza disponibile per l'indicizzazione della ricerca e solo in iOS, si rende inoltre il `AppLinkEntry` istanza disponibile per la distribuzione. Per altre informazioni sul passaggio di consegne, vedere [Introduzione a Handoff](~/ios/platform/handoff.md).

Il codice seguente viene illustrata l'impostazione di [ `AppLinkEntry.IsLinkActive` ](xref:Xamarin.Forms.IAppLinkEntry.IsLinkActive) proprietà `true` nel [ `Page.OnAppearing` ](xref:Xamarin.Forms.Page.OnAppearing) eseguire l'override:

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

Analogamente, quando la pagina rappresentata da un collegamento diretto dell'utente esce dall'applicazione, il [ `AppLinkEntry.IsLinkActive` ](xref:Xamarin.Forms.IAppLinkEntry.IsLinkActive) può essere impostata su `false`. In iOS e Android, viene arrestata la [ `AppLinkEntry` ](xref:Xamarin.Forms.AppLinkEntry) istanza venga annunciato anche per l'indicizzazione della ricerca e in iOS solo, si interrompe la pubblicità di `AppLinkEntry` istanza per la distribuzione. Questa operazione può essere eseguita nella [ `Page.OnDisappearing` ](xref:Xamarin.Forms.Page.OnDisappearing) sostituire, come illustrato nell'esempio di codice seguente:

```csharp
protected override void OnDisappearing()
{
    if (appLink != null)
    {
        appLink.IsLinkActive = false;
    }
}
```

## <a name="providing-data-to-handoff"></a>Fornisce dati a Handoff

In iOS, i dati specifici dell'applicazione possono essere archiviati quando l'indicizzazione della pagina. Questo risultato viene ottenuto mediante l'aggiunta di dati per il [ `KeyValues` ](xref:Xamarin.Forms.IAppLinkEntry.KeyValues) raccolta, ovvero un `Dictionary<string, string>` per l'archiviazione di coppie chiave-valore usate nel passaggio di consegne. Esegui handoff è un modo per l'utente avviare un'attività in uno dei propri dispositivi e continuare tale attività in un'altra dei propri dispositivi (identificato dall'account iCloud dell'utente). Il codice seguente viene illustrato un esempio dell'archiviazione di coppie chiave-valore specifiche dell'applicazione:

```csharp
var pageLink = new AppLinkEntry
{
    ...
};
pageLink.KeyValues.Add("appName", App.AppName);
pageLink.KeyValues.Add("companyName", "Xamarin");
```

I valori archiviati nel [ `KeyValues` ](xref:Xamarin.Forms.IAppLinkEntry.KeyValues) raccolta verranno archiviato nei metadati per la pagina indicizzata e verranno ripristinata quando l'utente tocca un risultato di ricerca che contiene un collegamento diretto (o quando Handoff consente di visualizzare il contenuto in un altro eseguito l'accesso dispositivo).

Inoltre, è possibile specificare valori per le chiavi seguenti:

- `contentType` -un `string` che specifica l'identificatore di tipo uniforme il contenuto indicizzato. La convenzione consigliata da usare per questo valore è il nome del tipo della pagina che contiene il contenuto indicizzato.
- `associatedWebPage` -un `string` che rappresenta la pagina web per vedere se il contenuto indicizzato può essere visualizzato anche nel web o se l'applicazione supporta i collegamenti diretti di Safari.
- `shouldAddToPublicIndex` : una `string` di entrambi `true` o `false` che determina se aggiungere il contenuto indicizzato all'indice di cloud pubblico di Apple, che può quindi essere presentato agli utenti che non hanno installato l'applicazione nel proprio dispositivo iOS o meno. Tuttavia, solo perché il contenuto è stato impostato per l'indicizzazione pubblico, non significa che verrà automaticamente aggiunto all'indice di cloud pubblico di Apple. Per altre informazioni, vedere [indicizzazione della ricerca pubblico](~/ios/platform/search/nsuseractivity.md). Si noti che questa chiave deve essere impostata su `false` quando si aggiungono i dati personali per il [ `KeyValues` ](xref:Xamarin.Forms.IAppLinkEntry.KeyValues) raccolta.

> [!NOTE]
> Il `KeyValues` raccolta non viene usata nella piattaforma Android.

Per altre informazioni sul passaggio di consegne, vedere [Introduzione a Handoff](~/ios/platform/handoff.md).

## <a name="summary"></a>Riepilogo

Questo articolo ha illustrato come usare l'applicazione di indicizzazione e deep linking per rendere disponibili per la ricerca in dispositivi iOS e Android il contenuto dell'applicazione xamarin. Forms. L'indicizzazione dell'applicazione consente alle applicazioni per rimanere al passo dalla visualizzazione nei risultati della ricerca che in caso contrario, potrebbe essere dimenticati dopo l'utilizzo di alcune.

## <a name="related-links"></a>Collegamenti correlati

- [Collegamento Deep (esempio)](https://developer.xamarin.com/samples/xamarin-forms/deeplinking/)
- [Ricerca di API iOS](~/ios/platform/search/index.md)
- [App e il collegamento in Android 6.0](~/android/platform/app-linking.md)
- [AppLinkEntry](xref:Xamarin.Forms.AppLinkEntry)
- [IAppLinkEntry](xref:Xamarin.Forms.IAppLinkEntry)
- [IAppLinks](xref:Xamarin.Forms.IAppLinks)
