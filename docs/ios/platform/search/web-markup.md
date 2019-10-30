---
title: Eseguire ricerche con markup Web in Novell. iOS
description: Questo documento descrive come creare risultati di ricerca basati sul Web che si collegano a un'app Novell. iOS. Viene illustrato come abilitare l'indicizzazione del contenuto Web, rendendo individuabile il sito Web dell'app, usando banner di smart app, collegamenti universali e altro ancora.
ms.prod: xamarin
ms.assetid: 876315BA-2EF9-4275-AE33-A3A494BBF7FD
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/20/2017
ms.openlocfilehash: 3d5db2f060b59fc689bea99141342b0447ac8933
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73031521"
---
# <a name="search-with-web-markup-in-xamarinios"></a>Eseguire ricerche con markup Web in Novell. iOS

Per le app che forniscono accesso al contenuto tramite un sito Web (non solo dall'interno dell'app), il contenuto Web può essere contrassegnato con collegamenti speciali che verranno sottoposti a ricerca per indicizzazione da Apple e fornire deep linking all'app nel dispositivo iOS 9 dell'utente.

Se l'app per iOS supporta già deep linking per dispositivi mobili e nel sito Web sono stati presentati collegamenti profondi al contenuto all'interno dell'app, il crawler Web _Applebot_ di Apple indicizza questo contenuto e lo aggiunge automaticamente all'indice cloud:

[![](web-markup-images/webmarkup01.png "Cloud Index overview")](web-markup-images/webmarkup01.png#lightbox)

Apple esporterà questi risultati nei risultati della ricerca di Spotlight e di Safari.
Se l'utente tocca uno di questi risultati (in cui è installata l'app), questi verranno portati al contenuto nell'app:

[![](web-markup-images/webmarkup02.png "Deep linking from a website in search results")](web-markup-images/webmarkup02.png#lightbox)

## <a name="enabling-web-content-indexing"></a>Abilitazione dell'indicizzazione del contenuto Web

Sono necessari quattro passaggi per eseguire ricerche nel contenuto dell'app usando il markup web:

1. Assicurarsi che Apple possa individuare e indicizzare il sito Web dell'app definendolo come sito Web di **supporto** o **Marketing** in iTunes Connect.
2. Verificare che il sito Web dell'app includa il markup necessario per implementare deep linking per dispositivi mobili. Per ulteriori informazioni, vedere le sezioni seguenti.
3. Abilitare la gestione dei collegamenti profondi nell'app iOS.
4. Aggiungere markup per i dati strutturati emersi dal sito Web dell'app per fornire un risultato completo e accattivante all'utente finale. Questo passaggio non è strettamente necessario, ma è consigliato da Apple.

Le sezioni seguenti illustrano in modo dettagliato questi passaggi.

## <a name="make-your-apps-website-discoverable"></a>Rendere individuabile il sito Web dell'app

Il modo più semplice per trovare Apple nel sito Web dell'app è usarlo come sito Web di **supporto** o **Marketing** quando si invia l'app ad Apple tramite iTunes Connect.

## <a name="using-smart-app-banners"></a>Uso di banner di smart app

Fornire un banner app intelligente nel sito Web per presentare un collegamento chiaro nell'app. Se l'app non è già installata, Safari chiederà automaticamente all'utente di installare l'app. In caso contrario, l'uso può toccare il collegamento **Visualizza** per avviare l'app dal sito Web. Ad esempio, per creare un banner di smart app, è possibile usare il codice seguente:

```html
<meta name="AppName" content="app-id=123456, app-argument=http://company.com/AppName">
```

Per altre informazioni, vedere la documentazione di Apple [promoting Apps with Smart App banners](https://developer.apple.com/library/ios/documentation/AppleApplications/Reference/SafariWebContent/PromotingAppswithAppBanners/PromotingAppswithAppBanners.html) .

## <a name="using-universal-links"></a>Uso di collegamenti universali

Una novità di iOS 9, i collegamenti universali rappresentano un'alternativa migliore per i banner di smart app o gli schemi URL personalizzati esistenti, fornendo gli elementi seguenti:

- **Univoco** : lo stesso URL non può essere richiesto da più di un sito Web.
- **Sicuro** : è necessario un certificato firmato per il sito Web che garantisce che il sito Web sia di proprietà dell'utente e collegato in modo valido all'app.
- **Flessibile** : l'utente finale può controllare se l'URL avvia il sito Web o l'app.
- **Universale** : è possibile usare lo stesso URL per definire il contenuto del sito Web e dell'app.

## <a name="using-twitter-cards"></a>Uso di schede Twitter

È possibile fornire collegamenti profondi al contenuto dell'app tramite una scheda Twitter. Esempio:

```html
<meta name="twitter:app:name:iphone" content="AppName">
<meta name="twitter:app:id:iphone" content="AppNameID">
<meta name="twitter:app:url:iphone" content="AppNameURL">
```

Per ulteriori informazioni, vedere la documentazione relativa al protocollo Twitter per le [schede](https://developer.twitter.com/en/docs/tweets/optimize-with-cards/overview/abouts-cards) Twitter.

## <a name="using-facebook-app-links"></a>Uso di collegamenti all'app Facebook

È possibile fornire collegamenti profondi al contenuto dell'app usando un collegamento all'app Facebook. Esempio:

```html
<meta property="al:ios:app_name" content="AppName">
<meta property="al:ios:app_store_id" content="AppNameID">
<meta property="al:ios:url" content="AppNameURL">
```

Per ulteriori informazioni, vedere la documentazione sui [collegamenti alle app](https://developers.facebook.com/docs/applinks) di Facebook.

## <a name="opening-deep-links"></a>Apertura di collegamenti profondi

È necessario aggiungere il supporto per l'apertura e la visualizzazione di collegamenti profondi nell'app Novell. iOS. Modificare il file **AppDelegate.cs** ed eseguire l'override del metodo `OpenURL` per gestire il formato dell'URL personalizzato. Esempio:

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

Nel codice precedente, si cerca un URL contenente `/appname` e si passa il valore di `query` (`123` in questo esempio) a un controller di visualizzazione personalizzato nell'app per visualizzare il contenuto richiesto all'utente.

## <a name="providing-rich-results-with-structured-data"></a>Offrire risultati avanzati con dati strutturati

Includendo il markup dei dati strutturati, è possibile fornire risultati di ricerca avanzati all'utente finale che vanno oltre semplicemente un titolo e una descrizione. Includere immagini, dati specifici dell'app, ad esempio classificazioni, e azioni per i risultati usando markup di dati strutturati.

I risultati avanzati sono più accattivanti e possono contribuire a migliorare la classificazione nell'indice di ricerca basato sul cloud inducendo più utenti a interagire con loro.

Un'opzione per fornire markup di dati strutturati consiste nell'usare il grafo aperto. Esempio:

```html
<meta property="og:image" content="http://company.com/appname/icon.jpg">
<meta property="og:audio" content="http://company.com/appname/theme.m4a">
<meta property="og:video" content="http://company.com/appname/tutorial.mp4">
```

Per ulteriori informazioni, vedere il sito Web di [Open Graph](https://ogp.me) .

Un altro formato comune per il markup dei dati strutturati è il formato di microdati di schema. org. Esempio:

```html
<div itemprop="aggregateRating" itemscope itemtype="http://schema.org/AggregateRating">
  <span itemprop="ratingValue">4** stars -
  <span itemprop="reviewCount">255** reviews
```

Le stesse informazioni possono essere rappresentate nel formato JSON-LD di schema. org:

```html
<script type="application/ld+json">
  "@content":"http://schema.org",
  "@type":"AggregateRating",
  "ratingValue":"4",
  "reviewCount":"255"
</script>
```

Di seguito viene illustrato un esempio di metadati del sito Web che forniscono risultati di ricerca avanzati all'utente finale:

[![](web-markup-images/deeplink01.png "Rich search results via Structured Data Markup")](web-markup-images/deeplink01.png#lightbox)

Apple attualmente supporta i tipi di schema seguenti da schema.org:

- AggregateRating
- ImageObject
- InteractionCount
- Offre
- Organizzazione
- PriceRange
- Ricetta
- SearchAction

Per ulteriori informazioni su questi tipi di schema, vedere [schema.org](https://schema.org).

## <a name="providing-actions-with-structured-data"></a>Fornire azioni con dati strutturati

Tipi specifici di dati strutturati consentiranno a un risultato della ricerca di essere azionabile dall'utente finale. Attualmente sono supportate le azioni seguenti:

- Comporre un numero di telefono.
- Recupero della direzione mappa per un determinato indirizzo.
- Riproduzione di un file audio o video.

Ad esempio, la definizione di un'azione per comporre un numero di telefono potrebbe essere simile alla seguente:

```html
<div itemscope itemtype="http://schema.org/Organization">
  <span itemprop="telephone">(408) 555-1212**
```

Quando il risultato della ricerca viene presentato all'utente finale, nel risultato viene visualizzata una piccola icona del telefono. Se l'utente tocca l'icona, verrà chiamato il numero specificato.

Il codice HTML seguente aggiunge un'azione per riprodurre un file audio dai risultati della ricerca:

```html
<div itemscope itemtype="http://schema.org/AudioObject">
  <span itemprop="contentUrl">http://company.com/appname/greeting.m4a**
```

Infine, il codice HTML seguente aggiunge un'azione per ottenere le direzioni dai risultati della ricerca:

```html
<div itemscope itemtype="http://schema.org/PostalAddress">
  <span itemprop="streetAddress">1 Infinite Loop**
  <span itemprop="addressLocality">Cupertino**
  <span itemprop="addressRegion">CA**
  <span itemprop="postalCode">95014**
```

Per altre informazioni, vedere il sito per [sviluppatori di ricerca app](https://developer.apple.com/ios/search/)di Apple.

## <a name="related-links"></a>Collegamenti correlati

- [Esempi di iOS 9](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+iOS9)
- [iOS 9 per sviluppatori](https://developer.apple.com/ios/pre-release/)
- [iOS 9,0](https://developer.apple.com/library/prerelease/ios/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html)
- [Guida alla programmazione di app Search](https://developer.apple.com/library/prerelease/ios/documentation/General/Conceptual/AppSearch/index.html#//apple_ref/doc/uid/TP40016308)
