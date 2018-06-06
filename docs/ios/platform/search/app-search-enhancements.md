---
title: Miglioramenti della ricerca di App in xamarin. IOS
description: Questo articolo descrive i miglioramenti Apple stabilita per la ricerca di App in iOS 10 e come implementarli in xamarin. IOS.
ms.prod: xamarin
ms.assetid: 30124DB6-6A02-4F66-A2D9-BBC8008E6B48
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/15/2017
ms.openlocfilehash: 06c405a15c26e02908d609bc27cac2c0509e5028
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/05/2018
ms.locfileid: "34787884"
---
# <a name="app-search-enhancements-in-xamarinios"></a>Miglioramenti della ricerca di App in xamarin. IOS

_Questo articolo descrive i miglioramenti Apple stabilita per la ricerca di App in iOS 10 e come implementarli in xamarin. IOS._

In iOS 10, Apple ha numerosi miglioramenti alla ricerca di App, ad esempio il collegamento diretto Crowdsourced, la ricerca In-App, continuazione di ricerca e visualizzazione dei risultati della convalida. Questo articolo vengono descritte l'implementazione di queste funzionalità in un'app xamarin. IOS.

## <a name="about-app-search-enhancements"></a>Sui miglioramenti apportati alla ricerca di App

Core Spotlight in iOS 10 offre vari miglioramenti per la ricerca di App, ad esempio:

- **Crowdsourced con collegamento diretto popolarità (con privacy differenziale)** -consente di promuovere i contenuti dell'app con collegamento diretto nei risultati della ricerca.
- **La ricerca in-App** -usare il nuovo `CSSearchQuery` classe per fornire capacità di ricerca Spotlight nell'applicazione simile al funzionano delle app di posta elettronica, messaggi e le note.
- **Ricerca di continuazione** : consente di avviare una ricerca Spotlight o Safari, quindi aprire un'app e continuare la ricerca.
- **Visualizzazione dei risultati della convalida** -Apple [lo strumento di convalida API di ricerca di App](https://search.developer.apple.com/appsearch-validation-tool) viene visualizzata una rappresentazione visiva del markup di un sito Web e il collegamento diretto quando preforming test.
- **Messaggio App immagine condivisione** -consente comuni delle immagini nell'applicazione previsti nelle ricerche in evidenza la condivisione in messaggi (tramite un messaggio dell'estensione App).

Nelle sezioni seguenti verranno illustrate più dettagliatamente questi argomenti.

## <a name="crowdsourced-deep-link-popularity"></a>Popolarità Crowdsourced con collegamento diretto

iOS 10 fornisce un meccanismo per il conteggio di frequenza che collegamenti deep più comuni in un'app vengono seguiti dall'utente e utilizza queste informazioni per migliorare la classificazione di un'app del contenuto nei risultati della ricerca, proteggendo al contempo l'identità dell'utente tramite  *Privacy differenziale*.

Per app che usano `NSUserActivity` oggetti fornire gli URL con collegamento diretto e avere la `EligibleForPublicIndexing` proprietà impostata su `true`, iOS 10 invia un subset di *differenziale Privacy hash* per i server Apple. Queste informazioni viene quindi utilizzate per alzare di livello più diffuso contenuto all'interno dell'applicazione nei risultati della ricerca.

Per ulteriori informazioni sull'implementazione per il collegamento diretto in un'app xamarin. IOS, consultare il nostro [ricerca con NSUserActivity](~/ios/platform/search/nsuseractivity.md) documentazione.

## <a name="in-app-searching"></a>La ricerca in-App

Implementando il nuovo [CSSearchQuery](https://developer.apple.com/reference/corespotlight/cssearchquery) (classe), un'app consentono di Spotlight ricerca e la tecnologia regola corrispondente per trovare contenuti all'interno di se stesso, senza che sia necessario lasciare l'app (simile a come la posta elettronica, messaggi e le note app lavoro).

In genere, le applicazioni che supportano `CSSearchQuery` non è necessario gestire i propri, indice di ricerca separata. 

## <a name="search-continuation"></a>Continuazione di ricerca

In iOS 9, Apple ha introdotto le API di ricerca (ad esempio Core Spotlight, `NSUserActivity` e markup web) per fornire profondità-desiderato del contenuto all'interno di un'app per consentire agli utenti di eseguire la ricerca di tale contenuto utilizzando le interfacce di ricerca Spotlight sia Safari. Vedere il nostro [nuove API di ricerca](~/ios/platform/search/index.md) documentazione per maggiori dettagli.

In iOS i 10 Apple si basa su questa funzionalità, consentendo all'utente di avviare una ricerca Spotlight o Safari, quindi continuare la ricerca all'apertura di un'app. 

Per implementare questa funzionalità, modificare l'app `Info.plist` file, aggiungere il `CoreSpotlightContinuation` chiave di tipo **booleano** e impostarne il valore su `YES`:

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

[![](app-search-enhancements-images/search01.png "Modifica CoreSpotlightContinuation nel file Info. plist")](app-search-enhancements-images/search01.png#lightbox)

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![](app-search-enhancements-images/searchw01.png "Modifica CoreSpotlightContinuation nel file Info. plist")](app-search-enhancements-images/search01.png#lightbox)

-----

Per rispondere all'utente di continuare un risultato di ricerca (`NSUserActivity`), modificare il `AppDelegate.cs` file ed eseguire l'override di `ContinueUserActivity` metodo. Ad esempio:

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

Questo codice cerca il tipo di azione di continuazione di query (`userActivity.ActivityType == CSSearchQuery.ContinuationActionType`), quindi legge la query corrente dell'utente dal `NSUserActivity` dizionario di informazioni utente della classe (`userActivity.UserInfo.KeyForValue(CSSearchQuery.QueryString)`). A questo punto, l'app deve eseguire l'azione per continuare la ricerca dell'utente.

Per ulteriori informazioni sull'utilizzo con ricerche in un'app xamarin. IOS, consultare il nostro [ricerca Spotlight di Core](~/ios/platform/search/corespotlight.md) documentazione.

## <a name="visualization-of-validation-results"></a>Visualizzazione dei risultati della convalida

Apple [lo strumento di convalida API di ricerca di App](https://search.developer.apple.com/appsearch-validation-tool) ora consente di visualizzare una rappresentazione visiva del markup di un sito Web e il collegamento completo (incluso il markup, ad esempio definiti [Schema.org](http://schema.org/)) quando preforming test.

Tramite lo strumento di convalida, uno sviluppatore può vedere le informazioni che il Crawler Web Applebot è indicizzato per il sito, ad esempio titolo, descrizione, URL e altri elementi supportati.

Per ulteriori informazioni sull'uso di Markup Web, vedere il nostro [Cerca con Markup Web](~/ios/platform/search/web-markup.md) documentazione.

## <a name="message-app-image-sharing"></a>Immagine di App di condivisione dei messaggi

Se un messaggio dell'estensione App offre immagini per la condivisione di messaggi, l'estensione possa essere configurati per consentire all'utente di eseguire ricerche in primo piano per le immagini più diffuse all'interno dei messaggi, senza la necessità di lasciare l'app.

Per abilitare questa funzionalità, eseguire le operazioni seguenti:

1. Creare un'estensione di App di messaggio.
2. Aggiungere il `com.apple.developer.associated-domains` ai diritti dell'app e includono un elenco di domini web che ospitano le immagini dell'estensione App il messaggio è la condivisione. Per ogni dominio, specificare il `spotlight-image-search` servizio.
3. Aggiungere un `apple-app-site-association` file al sito Web che ospita le immagini. Questo file include un dizionario per la `spotlight-image-search` service e include l'ID dell'app, che è il prefisso ID Team o ID dell'App aggiungendo l'ID del pacchetto. Il file può contenere fino a 500 percorsi e i modelli che verranno indicizzati da Spotlight e inclusi nella ricerca di immagine comuni. Per ulteriori informazioni, vedere Apple [creare e caricare il File di associazione](https://developer.apple.com/library/prerelease/content/documentation/General/Conceptual/AppSearch/UniversalLinks.html#//apple_ref/doc/uid/TP40016308-CH12-SW4) documentazione.
4. Consenti Applebot ricerca per indicizzazione i siti Web. Vedere Apple [su Applebot](https://support.apple.com/HT204683) documentazione.

Vedere il nostro [messaggio App integrazione](~/ios/platform/message-app-integration/index.md) documentazione per maggiori dettagli.

## <a name="summary"></a>Riepilogo

In questo articolo è illustrati i miglioramenti Apple stabilita per la ricerca di App in iOS 10 e come implementarli in xamarin. IOS.



## <a name="related-links"></a>Collegamenti correlati

- [Esempi di iOS 10](https://developer.xamarin.com/samples/ios/iOS10/)
