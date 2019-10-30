---
title: Miglioramenti della ricerca di app in Novell. iOS
description: Questo articolo descrive i miglioramenti apportati da Apple alla ricerca di app in iOS 10 e come implementarli in Novell. iOS.
ms.prod: xamarin
ms.assetid: 30124DB6-6A02-4F66-A2D9-BBC8008E6B48
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/15/2017
ms.openlocfilehash: f0d638d566290dd2ae0d8453133ee340d5b4ce3f
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73031588"
---
# <a name="app-search-enhancements-in-xamarinios"></a>Miglioramenti della ricerca di app in Novell. iOS

_Questo articolo descrive i miglioramenti apportati da Apple alla ricerca di app in iOS 10 e come implementarli in Novell. iOS._

In iOS 10, Apple ha apportato diversi miglioramenti alla ricerca di app, come il Deep linking con crowdsourcing, la ricerca in-app, la continuazione della ricerca e la visualizzazione dei risultati della convalida. In questo articolo viene illustrata l'implementazione di queste funzionalità in un'app Novell. iOS.

## <a name="about-app-search-enhancements"></a>Informazioni sui miglioramenti apportati alla ricerca app

I principali Spotlight in iOS 10 forniscono diversi miglioramenti alla ricerca di app, ad esempio:

- **Popolarità del collegamento di crowdsourcing (con privacy differenziale)** : offre un modo per promuovere il contenuto delle app con collegamento profondo nei risultati della ricerca.
- **Ricerca in-app** : usare la nuova classe `CSSearchQuery` per fornire funzionalità di ricerca Spotlight in-app simili a quelle del funzionamento delle app di posta elettronica, messaggi e note.
- **Continuazione ricerca** : consente a un utente di avviare una ricerca in Spotlight o Safari, quindi aprire un'app e continuare la ricerca.
- **Visualizzazione dei risultati della convalida** : [lo strumento di convalida dell'API ricerca app](https://search.developer.apple.com/appsearch-validation-tool) di Apple ora Visualizza una rappresentazione visiva del markup di un sito Web e il collegamento profondo quando si preformano i test.
- **Condivisione immagini app messaggio** : consente di visualizzare le immagini in-app più diffuse fornite per la condivisione nei messaggi (tramite un'estensione di app per i messaggi) nelle ricerche Spotlight.

Le sezioni seguenti illustrano in modo più dettagliato questi argomenti.

## <a name="crowdsourced-deep-link-popularity"></a>Popolarità con collegamento profondo crowdsourcing

iOS 10 fornisce un meccanismo per contare la frequenza con cui i collegamenti profondi più diffusi in un'app sono seguiti dall'utente e usano queste informazioni per migliorare la classificazione del contenuto di un'app nei risultati della ricerca, proteggendo al tempo stesso l'identità dell'utente usando il *differenziale Privacy*.

Per le app che usano oggetti `NSUserActivity` per fornire URL con collegamento profondo e che la proprietà `EligibleForPublicIndexing` è impostata su `true`, iOS 10 Invia un subset di *hash di privacy differenziale* ai server Apple. Queste informazioni vengono quindi usate per innalzare di livello i contenuti in-app più diffusi nei risultati della ricerca.

Per altre informazioni sull'implementazione del Deep linking in un'app Novell. iOS, vedere la documentazione relativa [alla ricerca con NSUserActivity](~/ios/platform/search/nsuseractivity.md) .

## <a name="in-app-searching"></a>Ricerca in-app

Implementando la nuova classe [CSSearchQuery](https://developer.apple.com/reference/corespotlight/cssearchquery) , un'app può fornire la tecnologia di ricerca e di corrispondenza di Spotlight per trovare il contenuto all'interno di se stesso, senza che l'utente debba uscire dall'app, in modo analogo al funzionamento dell'app posta elettronica, messaggi e note.

In genere, le app che supportano `CSSearchQuery` non dovranno gestire un indice di ricerca separato.

## <a name="search-continuation"></a>Continuazione ricerca

In iOS 9, Apple ha introdotto le API di ricerca (ad esempio Spotlight principale, `NSUserActivity` e markup Web) per fornire il contenuto in modo approfondito all'interno di un'app, in modo da consentire agli utenti di cercare tale contenuto usando le interfacce di ricerca Spotlight e Safari. Per altri dettagli, vedere la nuova documentazione sulle [API di ricerca](~/ios/platform/search/index.md) .

In iOS 10 Apple si basa su questa funzionalità consentendo all'utente di avviare una ricerca in Spotlight o Safari, quindi continuare la ricerca quando aprono un'app.

Per implementare questa funzionalità, modificare il file di `Info.plist` dell'app, aggiungere la chiave `CoreSpotlightContinuation` di tipo **booleano** e impostarne il valore su `YES`:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

[![](app-search-enhancements-images/search01.png "Editing CoreSpotlightContinuation in the Info.plist file")](app-search-enhancements-images/search01.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![](app-search-enhancements-images/searchw01.png "Editing CoreSpotlightContinuation in the Info.plist file")](app-search-enhancements-images/search01.png#lightbox)

-----

Per rispondere all'utente che continua un risultato della ricerca (`NSUserActivity`), modificare il file di `AppDelegate.cs` ed eseguire l'override del metodo di `ContinueUserActivity`. Esempio:

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

Questo codice cerca il tipo di azione di continuazione di query (`userActivity.ActivityType == CSSearchQuery.ContinuationActionType`), quindi legge la query corrente dell'utente dal dizionario informazioni utente della classe `NSUserActivity` (`userActivity.UserInfo.KeyForValue(CSSearchQuery.QueryString)`). Da qui, l'app deve intervenire per continuare la ricerca dell'utente.

Per altre informazioni sull'uso delle ricerche in un'app Novell. iOS, vedere la documentazione relativa [alla ricerca con core Spotlight](~/ios/platform/search/corespotlight.md) .

## <a name="visualization-of-validation-results"></a>Visualizzazione dei risultati della convalida

[Lo strumento di convalida API ricerca app](https://search.developer.apple.com/appsearch-validation-tool) di Apple ora Visualizza una rappresentazione visiva del markup di un sito Web e il collegamento Deep (incluso markup come definito in [schema.org](https://schema.org/)) quando si preformano i test.

Utilizzando lo strumento di convalida, uno sviluppatore può visualizzare le informazioni indicizzate da Applebot web crawler per il sito, ad esempio titolo, descrizione, URL e altri elementi supportati.

Per ulteriori informazioni sull'utilizzo del markup web, vedere la documentazione relativa a [Cerca con markup web](~/ios/platform/search/web-markup.md) .

## <a name="message-app-image-sharing"></a>Condivisione immagini app messaggio

Se un'estensione dell'app messaggio fornisce immagini per la condivisione nei messaggi, l'estensione può essere configurata in modo da consentire all'utente di eseguire ricerche Spotlight per le immagini più diffuse dall'interno dei messaggi, senza dover uscire dall'app.

Per abilitare questa funzionalità, eseguire le operazioni seguenti:

1. Creare un'estensione di app per i messaggi.
2. Aggiungere la `com.apple.developer.associated-domains` ai diritti dell'app e includere un elenco di domini Web che ospitano le immagini condivise dall'estensione dell'app per i messaggi. Per ogni dominio specificare il servizio `spotlight-image-search`.
3. Aggiungere un file di `apple-app-site-association` al sito Web che ospita le immagini. Questo file include un dizionario per il servizio `spotlight-image-search` e include l'ID dell'app, ovvero l'ID del team o il prefisso dell'ID app seguito dall'ID del bundle. Il file può contenere fino a 500 percorsi e modelli che verranno indicizzati da Spotlight e inclusi nelle ricerche di immagini più diffuse. Per ulteriori informazioni, vedere la documentazione relativa [alla creazione e al caricamento del file di associazione](https://developer.apple.com/library/prerelease/content/documentation/General/Conceptual/AppSearch/UniversalLinks.html#//apple_ref/doc/uid/TP40016308-CH12-SW4) di Apple.
4. Consentire al Applebot di eseguire la ricerca per indicizzazione dei siti Web Vedere la documentazione di Apple relativa [a Applebot](https://support.apple.com/HT204683) .

Per altri dettagli, vedere la documentazione relativa all' [integrazione dell'app Message](~/ios/platform/message-app-integration/index.md) .

## <a name="summary"></a>Riepilogo

Questo articolo ha illustrato i miglioramenti apportati da Apple alla ricerca di app in iOS 10 e come implementarli in Novell. iOS.

## <a name="related-links"></a>Collegamenti correlati

- [Esempi di iOS 10](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+iOS10)
