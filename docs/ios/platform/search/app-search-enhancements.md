---
title: Miglioramenti della ricerca nelle App in xamarin. IOS
description: Questo articolo illustra i miglioramenti di Apple ha tentato di ricerca nelle App in iOS 10 e come implementarli in xamarin. IOS.
ms.prod: xamarin
ms.assetid: 30124DB6-6A02-4F66-A2D9-BBC8008E6B48
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/15/2017
ms.openlocfilehash: e61cb20f12f6373ef57beb759b933d3dd9b6e76e
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2018
ms.locfileid: "50110583"
---
# <a name="app-search-enhancements-in-xamarinios"></a>Miglioramenti della ricerca nelle App in xamarin. IOS

_Questo articolo illustra i miglioramenti di Apple ha tentato di ricerca nelle App in iOS 10 e come implementarli in xamarin. IOS._

In iOS 10, Apple ha apportato numerosi miglioramenti alla ricerca di App, ad esempio Crowdsourcing avanzato per il collegamento, la ricerca In-App, continuazione di ricerca e visualizzazione dei risultati della convalida. Questo articolo illustra l'implementazione di queste funzionalità in un'app xamarin. IOS.

## <a name="about-app-search-enhancements"></a>Sui miglioramenti apportati alla ricerca di App

Core Spotlight in iOS 10 offre numerosi miglioramenti alla ricerca di App, ad esempio:

- **Popolarità di collegamento diretto di Crowdsourcing (con privacy differenziale)** -fornisce un modo per promuovere il contenuto di app con collegamento diretto nei risultati della ricerca.
- **La ricerca in-App** -usare il nuovo `CSSearchQuery` classe per fornire capacità di ricerca Spotlight in-app simile al funzionamento delle app di posta elettronica, messaggi e note.
- **Eseguire la ricerca continuazione** : consente agli utenti di avviare una ricerca di Spotlight o Safari, quindi aprire un'app e continuare la ricerca.
- **Visualizzazione dei risultati della convalida** -Apple [lo strumento di convalida API di ricerca di App](https://search.developer.apple.com/appsearch-validation-tool) ora visualizza una rappresentazione visiva di markup e il collegamento diretto di un sito Web quando preforming test.
- **Messaggio condivisione immagine delle App** -consente di immagini in-app popolari fornite per la condivisione in messaggi (tramite un'estensione dell'App messaggi) deve essere visualizzato nella ricerca Spotlight.

Le sezioni seguenti vengano illustrano questi argomenti in modo più dettagliato.

## <a name="crowdsourced-deep-link-popularity"></a>Collegamento diretto Crowdsourcing popolarità

iOS 10 fornisce un meccanismo per contare la frequenza che i collegamenti avanzato più diffusi in un'app vengono seguiti dall'utente e Usa queste informazioni per migliorare la classificazione di un'app contenute nei risultati della ricerca, continuando comunque a proteggere l'identità dell'utente tramite  *Privacy differenziale*.

Per app che usano `NSUserActivity` gli oggetti per fornire l'URL di collegamento diretto e il `EligibleForPublicIndexing` proprietà impostata su `true`, iOS 10 invia un subset di *differenziale Privacy hash* per i server Apple. Queste informazioni viene quindi utilizzate per alzare di livello più diffusi contenuti all'interno dell'applicazione nei risultati della ricerca.

Per altre informazioni sull'implementazione per il collegamento diretto in un'app xamarin. IOS, vedere la [eseguire una ricerca con NSUserActivity](~/ios/platform/search/nsuseractivity.md) documentazione.

## <a name="in-app-searching"></a>La ricerca in-App

Implementando il nuovo [CSSearchQuery](https://developer.apple.com/reference/corespotlight/cssearchquery) classe, un'app può fornire ricerca di Spotlight e tecnologia basata su regole corrispondenti per trovare contenuti all'interno di se stesso, senza che l'utente di dover uscire dall'app (simile a come la posta elettronica, messaggi e note di app lavoro).

In genere, le app che supportano `CSSearchQuery` non necessario gestire i propri, indice di ricerca separate. 

## <a name="search-continuation"></a>Continuazione ricerca

In iOS 9 Apple ha introdotto le API di ricerca (ad esempio Core Spotlight, `NSUserActivity` e markup web) per fornire deep-piacere di contenuto all'interno di un'app per consentire agli utenti di eseguire la ricerca per il contenuto usando le interfacce di ricerca di Spotlight sia Safari. Vedere la [nuove API di ricerca](~/ios/platform/search/index.md) informazioni più dettagliate.

In iOS 10 Apple si basa su questa funzionalità, consentendo all'utente di avviare una ricerca di Spotlight o Safari, quindi continuare la ricerca quando si apre un'app. 

Per implementare questa funzionalità, modificare l'app `Info.plist` , aggiungere i `CoreSpotlightContinuation` chiavi di tipo **booleano** e impostarne il valore su `YES`:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

[![](app-search-enhancements-images/search01.png "Modifica CoreSpotlightContinuation nel file Info. plist")](app-search-enhancements-images/search01.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![](app-search-enhancements-images/searchw01.png "Modifica CoreSpotlightContinuation nel file Info. plist")](app-search-enhancements-images/search01.png#lightbox)

-----

Per rispondere all'utente di continuare un risultato di ricerca (`NSUserActivity`), modificare il `AppDelegate.cs` file ed eseguire l'override di `ContinueUserActivity` (metodo). Ad esempio:

```csharp
public override bool ContinueUserActivity (UIApplication application, NSUserActivity userActivity, UIApplicationRestorationHandler completionHandler)
{

    // Take action based on the activity type
    switch (userActivity.ActivityType) {
    case "com.xamarin.platform":
        // Restore the state of the app here...
        break;
    default:
        if (userActivity.ActivityType == CSSearchQuery.ContinuationActionType) {
            var search = userActivity.UserInfo.KeyForValue(CSSearchQuery.QueryString);
            // Continue user's search here...
        }
        break;
    }

    return true;
}
```

Questo codice cerca il tipo di azione di continuazione di query (`userActivity.ActivityType == CSSearchQuery.ContinuationActionType`), quindi legge query corrente dell'utente dal `NSUserActivity` dizionario info utente della classe (`userActivity.UserInfo.KeyForValue(CSSearchQuery.QueryString)`). A questo punto, l'app necessita intervenire per continuare la ricerca dell'utente.

Per altre informazioni sull'uso di ricerche in un'app xamarin. IOS, vedere la [eseguire una ricerca con Spotlight Core](~/ios/platform/search/corespotlight.md) documentazione.

## <a name="visualization-of-validation-results"></a>Visualizzazione dei risultati della convalida

Apple [lo strumento di convalida API di ricerca di App](https://search.developer.apple.com/appsearch-validation-tool) ora visualizza una rappresentazione visiva di markup e il collegamento diretto di un sito Web (incluso il markup come definito [Schema.org](http://schema.org/)) quando preforming test.

Usando lo strumento di convalida, uno sviluppatore può vedere le informazioni che il Crawler Web Applebot è indicizzato per il sito, ad esempio titolo, descrizione, URL e altri elementi è supportata.

Per altre informazioni sull'utilizzo dei Markup Web, vedere la [Cerca con Markup Web](~/ios/platform/search/web-markup.md) documentazione.

## <a name="message-app-image-sharing"></a>Immagine dell'App di condivisione del messaggio

Se un'estensione dell'App messaggi fornisce immagini per la condivisione nei messaggi, l'estensione può essere configurata per consentire all'utente di eseguire ricerche in primo piano per le immagini più diffuse all'interno di messaggi, senza dover uscire dall'app.

Per abilitare questa funzionalità, eseguire le operazioni seguenti:

1. Creare un'estensione dell'App messaggi.
2. Aggiungere il `com.apple.developer.associated-domains` uso degli Entitlement dell'app e includono un elenco di domini web che ospitano le immagini condividono l'estensione dell'App messaggi. Per ogni dominio, specificare il `spotlight-image-search` servizio.
3. Aggiungere un `apple-app-site-association` file al sito Web che ospita le immagini. Questo file include un dizionario per la `spotlight-image-search` service e include l'ID dell'app, che è il prefisso ID Team o un ID App aggiungendo l'ID del Bundle. Il file può contenere fino a 500 i percorsi e i modelli che saranno indicizzati tramite Spotlight e incluso nelle ricerche di immagini popolari. Per altre informazioni, vedere di Apple [creando e caricando il File di associazione](https://developer.apple.com/library/prerelease/content/documentation/General/Conceptual/AppSearch/UniversalLinks.html#//apple_ref/doc/uid/TP40016308-CH12-SW4) documentazione.
4. Consenti il Applebot una ricerca per indicizzazione i siti Web. Vedere di Apple [sulle Applebot](https://support.apple.com/HT204683) documentazione.

Vedere la [integrazione di App messaggio](~/ios/platform/message-app-integration/index.md) informazioni più dettagliate.

## <a name="summary"></a>Riepilogo

Questo articolo ha illustrato i miglioramenti di Apple ha tentato di ricerca nelle App in iOS 10 e come implementarli in xamarin. IOS.



## <a name="related-links"></a>Collegamenti correlati

- [Esempi di iOS 10](https://developer.xamarin.com/samples/ios/iOS10/)
