---
title: Ricerca con Markup Web in xamarin. IOS
description: Questo documento descrive come creare i risultati della ricerca basata sul web che collegamento a un'app xamarin. IOS. Descrive come abilitare l'indicizzazione, rendendo individuabile, usando le intestazioni di app intelligenti, i collegamenti universali e altro sito Web dell'app il contenuto web.
ms.prod: xamarin
ms.assetid: 876315BA-2EF9-4275-AE33-A3A494BBF7FD
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/20/2017
ms.openlocfilehash: 243408ce6e2236b75ea35dfd17633a9a24493c1b
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2018
ms.locfileid: "50102802"
---
# <a name="search-with-web-markup-in-xamarinios"></a>Ricerca con Markup Web in xamarin. IOS

Per le app che forniscono accesso ai contenuti tramite un sito web (non solo dall'interno dell'app), contenuto web può essere contrassegnato con collegamenti speciali che verranno indicizzati da Apple e forniscono il collegamento completo all'app nel dispositivo iOS 9 dell'utente.

Se l'app per iOS supporta già collegamento complete per dispositivi mobili e il sito Web presentati i collegamenti diretti al contenuto all'interno della tua app, Apple _Applebot_ crawler web verranno indicizzare il contenuto e aggiungerla automaticamente al relativo indice Cloud:

[![](web-markup-images/webmarkup01.png "Panoramica di indice del cloud")](web-markup-images/webmarkup01.png#lightbox)

Apple esporrà questi risultati nei risultati della ricerca di Spotlight e Safari.
Se l'utente tocca uno di questi risultati (e hanno installato l'app), che non verrà applicati al contenuto nell'app:

[![](web-markup-images/webmarkup02.png "Avanzato per i collegamenti da un sito Web nei risultati della ricerca")](web-markup-images/webmarkup02.png#lightbox)

## <a name="enabling-web-content-indexing"></a>Abilitazione dell'indicizzazione del contenuto Web

Esistono quattro passaggi necessari per rendere è contenuto dell'app disponibili per la ricerca utilizzando il Markup Web:

1. Assicurarsi che Apple consente di individuare e indicizza sito Web dell'app per la sua definizione come le **supporto tecnico** oppure **Marketing** sito Web in iTunes Connect.
2. Assicurarsi che siti Web dell'app contiene il markup necessario per implementare il collegamento completo per dispositivi mobili. Vedere le sezioni seguenti per altri dettagli.
3. Abilitare il deep link di gestione nell'app iOS.
4. Aggiungere il markup per i dati strutturati replicati dal sito Web dell'app per fornire un risultato avanzato e accattivante per l'utente finale. Questo passaggio non è strettamente necessario, si consiglia vivamente di Apple.

Le sezioni seguenti verranno esaminati i passaggi in dettaglio.

## <a name="make-your-apps-website-discoverable"></a>Rendere individuabile sito Web dell'App

È il modo più semplice avere Apple trova sito Web dell'app per usarlo come le **supporto** oppure **Marketing** sito Web quando si invia l'app ad Apple tramite iTunes Connect.

## <a name="using-smart-app-banners"></a>Usando le intestazioni di App intelligenti

Fornire un Banner di App intelligenti nel tuo sito Web per presentare un collegamento non crittografato nella tua app. Se l'app non è già installato, Safari viene automaticamente richiesto all'utente di installare l'app. In caso contrario, è possibile toccare l'uso di **vista** collegamento per avviare l'app dal sito Web. Per creare un Banner di App intelligenti, ad esempio, è possibile usare il codice seguente:

```xml
<meta name="AppName" content="app-id=123456, app-argument=http://company.com/AppName">
```

Per altre informazioni, vedere di Apple [innalzamento di livello di App con banner di App intelligenti](https://developer.apple.com/library/ios/documentation/AppleApplications/Reference/SafariWebContent/PromotingAppswithAppBanners/PromotingAppswithAppBanners.html) documentazione.

## <a name="using-universal-links"></a>Usando i collegamenti universali

Nuovo a iOS 9, i collegamenti universali forniscono un'alternativa migliore rispetto al banner di App intelligenti o schemi di URL personalizzati esistenti specificando quanto segue:

- **Univoco** – lo stesso URL non può essere richiesta da più di un sito Web.
- **Proteggere** : è necessario un certificato firmato per il sito Web che garantisce il sito Web è di proprietà da parte dell'utente e munita collegato per il l'app.
- **Flessibile** : l'utente finale può controllare se l'URL avvia il sito Web o app.
- **Universal** – lo stesso URL consente di definire il contenuto del sito Web sia dell'app.

## <a name="using-twitter-cards"></a>Usando le schede di Twitter

È possibile fornire collegamenti diretti al contenuto dell'app con una scheda di Twitter. Ad esempio:

```xml
<meta name="twitter:app:name:iphone" content="AppName">
<meta name="twitter:app:id:iphone" content="AppNameID">
<meta name="twitter:app:url:iphone" content="AppNameURL">
```

Per altre informazioni, vedere di Twitter [Twitter protocollo Card](http://dev.twitter.com/cards/mobile) documentazione.

## <a name="using-facebook-app-links"></a>Utilizzo di collegamenti di App Facebook

È possibile fornire collegamenti diretti al contenuto dell'app con un App Link Facebook. Ad esempio:

```xml
<meta property="al:ios:app_name" content="AppName">
<meta property="al:ios:app_store_id" content="AppNameID">
<meta property="al:ios:url" content="AppNameURL">
```

Per altre informazioni, vedere di Facebook [collegamenti App](http://applinks.org) documentazione.

## <a name="opening-deep-links"></a>Apertura di collegamenti diretti

È necessario aggiungere il supporto per l'apertura e visualizzazione di collegamenti diretti nell'app xamarin. IOS. Modificare il **AppDelegate.cs** del file ed eseguire l'override di `OpenURL` metodo per gestire il formato dell'URL personalizzato. Ad esempio:

```csharp
public override bool OpenUrl (UIApplication application, NSUrl url, string sourceApplication, NSObject annotation)
{

    // Handling a URL in the form http://company.com/appname/?123
    try {
        var components = new NSUrlComponents(url,true);
        var path = components.Path;
        var query = components.Query;

        // Is this a known format?
        if (path == "/appname") {
            // Display the view controller for the content
            // specified in query (123)
            return ContentViewController.LoadContent(query);
        }
    } catch {
        // Ignore issue for now
    }

    return false;
}
```

Nel codice precedente, stiamo cercando un URL contenente `/appname` e passando il valore di `query` (`123` in questo esempio) a un controller di visualizzazione personalizzata nell'app per visualizzare il contenuto richiesto all'utente.

## <a name="providing-rich-results-with-structured-data"></a>Che fornisce risultati avanzati con dati strutturati

Includendo Markup dati strutturati è possibile fornire i risultati della ricerca avanzata per l'utente finale che vanno oltre semplicemente un titolo e descrizione. Includere immagini, i dati specifici delle app (ad esempio classificazioni) e le azioni per i risultati utilizzando il Markup di dati strutturati.

Risultati avanzati sono coinvolgere più e consentono di migliorare la classificazione nel Cloud basato su indice di ricerca inducendo più agli utenti di interagire con essi.

Una delle opzioni per fornire Markup dati strutturati consiste nell'usare Open Graph. Ad esempio:

```xml
<meta property="og:image" content="http://company.com/appname/icon.jpg">
<meta property="og:audio" content="http://company.com/appname/theme.m4a">
<meta property="og:video" content="http://company.com/appname/tutorial.mp4">
```

Per altre informazioni, vedere la [Open Graph](http://ogp.me) sito Web.

Un altro formato comune per il Markup di dati strutturati è microdati del schema.org. Ad esempio:

```xml
<div itemprop="aggregateRating" itemscope itemtype="http://schema.org/AggregateRating">
    <span itemprop="ratingValue">4** stars -
    <span itemprop="reviewCount">255** reviews


```

Le stesse informazioni possono essere rappresentate in formato JSON-LD del schema.org:

```xml
<script type="application/ld+json">
    "@content":"http://schema.org",
    "@type":"AggregateRating",
    "ratingValue":"4",
    "reviewCount":"255"
</script>
```

Di seguito illustra un esempio dei metadati dal sito Web che fornisce i risultati della ricerca avanzata per l'utente finale:

[![](web-markup-images/deeplink01.png "I risultati tramite Markup dati strutturati di ricerca avanzata")](web-markup-images/deeplink01.png#lightbox)

Apple supporta attualmente i seguenti tipi di schema schema.org:

 - AggregateRating
 - ImageObject
 - InteractionCount
 - Offerte
 - Organizzazione
 - Pricerange=under5dollars
 - File recipe
 - SearchAction

Per altre informazioni su questi tipi di schema, vedere [schema.org](http://schema.org).

## <a name="providing-actions-with-structured-data"></a>Che fornisce le azioni con dati strutturati

Tipi specifici di dati strutturati consentirà un risultato della ricerca per essere utilizzabili dall'utente finale. Attualmente sono supportate le azioni seguenti:

 - Comporre il numero di telefono.
 - Ottiene la direzione della mappa su un determinato indirizzo.
 - Riproduzione di un file audio o video.

Definizione di un'azione per comporre un numero di telefono, ad esempio, potrebbe essere simile al seguente:

```xml
<div itemscope itemtype="http://schema.org/Organization">
    <span itemprop="telephone">(408) 555-1212**


```

Quando il risultato della ricerca viene presentato all'utente finale, un'icona piccola phone verrà visualizzata nel risultato. Se l'utente tocca l'icona, verrà chiamato il numero specificato.

Il codice HTML seguente è necessario aggiungere un'azione per riprodurre un file audio dai risultati della ricerca:

```xml
<div itemscope itemtype="http://schema.org/AudioObject">
    <span itemprop="contentUrl">http://company.com/appname/greeting.m4a**


```

Infine, il codice HTML seguente aggiungerà un'azione per ottenere le indicazioni dai risultati della ricerca:

```xml
<div itemscope itemtype="http://schema.org/PostalAddress">
    <span itemprop="streetAddress">1 Infinite Loop**
    <span itemprop="addressLocality">Cupertino**
    <span itemprop="addressRegion">CA**
    <span itemprop="postalCode">95014**


```

Per altre informazioni, vedere di Apple [sito per sviluppatori di App ricerca](http://developer.apple.com/ios/search/).



## <a name="related-links"></a>Collegamenti correlati

- [Esempi di iOS 9](https://developer.xamarin.com/samples/ios/iOS9/)
- [per gli sviluppatori iOS 9](https://developer.apple.com/ios/pre-release/)
- [iOS 9.0](https://developer.apple.com/library/prerelease/ios/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html)
- [Guida alla programmazione di ricerca nelle App](https://developer.apple.com/library/prerelease/ios/documentation/General/Conceptual/AppSearch/index.html#//apple_ref/doc/uid/TP40016308)
