---
title: Ricerca con Markup Web
description: "Aggiunta di risultati della ricerca basata sul web che può essere collegata all'app."
ms.topic: article
ms.prod: xamarin
ms.assetid: 876315BA-2EF9-4275-AE33-A3A494BBF7FD
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/20/2017
ms.openlocfilehash: 63bc1f0ed13fe65b36e95978da9ccc2ea8d4481c
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2018
---
# <a name="search-with-web-markup"></a>Ricerca con Markup Web

Per le applicazioni che forniscono l'accesso ai contenuti tramite un sito web (non solo all'interno dell'app), il contenuto web può essere contrassegnato con collegamenti speciali che verranno indicizzati da Apple e forniscono il collegamento completo per l'app nel dispositivo iOS 9 dell'utente.

Se l'app iOS già supporta il collegamento di complete per dispositivi mobili e il sito Web presentati collegamenti diretti al contenuto all'interno, Apple dell'app _Applebot_ crawler web verrà indicizzare il contenuto e aggiungere automaticamente al relativo indice Cloud:

[ ![](web-markup-images/webmarkup01.png "Panoramica del cloud indice")](web-markup-images/webmarkup01.png)

Apple esporrà i risultati nei risultati della ricerca Spotlight e Safari.
Se l'utente tocca su uno di questi risultati (e hanno installato app), quindi verranno adottate per il contenuto dell'App:

[ ![](web-markup-images/webmarkup02.png "Completa il collegamento da un sito Web nei risultati della ricerca")](web-markup-images/webmarkup02.png)

## <a name="enabling-web-content-indexing"></a>Abilitazione dell'indicizzazione del contenuto Web

Vi sono quattro i passaggi necessari per verificare il contenuto dell'applicazione disponibile per la ricerca utilizzando il Markup Web:

1. Verificare che Apple consente di individuare e indicizzare sito Web dell'app, è necessario che lo definisce come il **supporto** o **Marketing** sito Web in iTunes Connect.
2. Accertarsi che il markup richiesto per implementare il collegamento completo mobili sito Web dell'app. Vedere le sezioni per ulteriori dettagli.
3. Abilitare collegamento diretto di gestione delle app iOS.
4. Aggiungere il markup per i dati strutturati rese disponibili dal sito Web dell'applicazione per fornire un risultato avanzato e interessante per l'utente finale. Durante questo passaggio non è strettamente necessario, è consigliabile da Apple.

Nelle sezioni seguenti verranno esaminate queste fasi.

## <a name="make-your-apps-website-discoverable"></a>Rendere individuabile sito Web dell'App

È il modo più semplice trovare il sito Web dell'app di Apple per utilizzarlo come il **supporto** o **Marketing** sito Web quando si invia l'app in Apple tramite iTunes Connect.

## <a name="using-smart-app-banners"></a>Usando intestazioni Smart App

Fornire un Banner di App Smart nel sito Web per presentare un collegamento deseleziona nell'app. Se l'app non è già installato, Safari automaticamente richiederà all'utente di installare l'app. In caso contrario è possibile toccare l'utilizzo di **vista** collegamento per avviare l'app dal sito Web. Per creare un Banner di App intelligente, ad esempio, è possibile utilizzare il codice seguente:

```xml
<meta name="AppName" content="app-id=123456, app-argument=http://company.com/AppName">
```

Per ulteriori informazioni, vedere Apple [innalzamento di livello App con intestazioni App Smart](https://developer.apple.com/library/ios/documentation/AppleApplications/Reference/SafariWebContent/PromotingAppswithAppBanners/PromotingAppswithAppBanners.html) documentazione.

## <a name="using-universal-links"></a>Utilizzo di collegamenti universali

Nuovo a iOS 9, collegamenti Universal offrono un'alternativa migliore a Smart banner App o schemi di URL personalizzati esistenti, fornendo le operazioni seguenti:

- **Univoco** : lo stesso URL non può essere richiesto da più di un sito Web.
- **Secure** : un certificato firmato è necessario per il sito Web che garantisce il sito Web è di proprietà da parte dell'utente e valida collegato per l'app.
- **Flessibile** : l'utente finale è possibile controllare se l'URL consente di avviare il sito Web o app.
- **Universal** : lo stesso URL consente di definire il contenuto del sito Web sia l'app.

## <a name="using-twitter-cards"></a>Uso delle carte di Twitter

È possibile fornire collegamenti diretti al contenuto dell'applicazione utilizzando una scheda di Twitter. Ad esempio:

```xml
<meta name="twitter:app:name:iphone" content="AppName">
<meta name="twitter:app:id:iphone" content="AppNameID">
<meta name="twitter:app:url:iphone" content="AppNameURL">
```

Per ulteriori informazioni, vedere del Twitter [Twitter protocollo Card](http://dev.twitter.com/cards/mobile) documentazione.

## <a name="using-facebook-app-links"></a>Utilizzo di collegamenti di App Facebook

È possibile fornire collegamenti diretti al contenuto dell'applicazione utilizzando un App Link Facebook. Ad esempio:

```xml
<meta property="al:ios:app_name" content="AppName">
<meta property="al:ios:app_store_id" content="AppNameID">
<meta property="al:ios:url" content="AppNameURL">
```

Per ulteriori informazioni, vedere Facebook [collegamenti App](http://applinks.org) documentazione.

## <a name="opening-deep-links"></a>Apertura collegamenti diretti

È necessario aggiungere il supporto per l'apertura e visualizzazione di collegamenti diretti in app xamarin. IOS. Modificare il **appdelegate. cs** file ed eseguire l'override di `OpenURL` metodo per gestire il formato dell'URL personalizzato. Ad esempio:

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

Nel codice precedente, si sta cercando un URL contenente `/appname` e passando il valore di `query` (`123` in questo esempio) a un controller di visualizzazione personalizzata nella nostra app per visualizzare il contenuto richiesto all'utente.

## <a name="providing-rich-results-with-structured-data"></a>Fornendo risultati Rich con dati strutturati

Incluso il Markup dati strutturata è possibile garantire i risultati della ricerca avanzata per l'utente finale che vanno oltre semplicemente un titolo e descrizione. Includere immagini e dati specifici dell'app (ad esempio classificazioni) e azioni da risultati utilizzando il Markup di dati strutturati.

Risultati Rich sono divertendosi e consentono di migliorare la classificazione nel Cloud basato su indice di ricerca inducendo più utenti di interagire con essi.

Un'opzione per fornire Markup dati strutturati è tramite Open Graph. Ad esempio:

```xml
<meta property="og:image" content="http://company.com/appname/icon.jpg">
<meta property="og:audio" content="http://company.com/appname/theme.m4a">
<meta property="og:video" content="http://company.com/appname/tutorial.mp4">
```

Per ulteriori informazioni, vedere il [Open Graph](http://ogp.me) sito Web.

Un altro formato comune per il Markup di dati strutturati è microdati del schema.org. Ad esempio:

```xml
<div itemprop="aggregateRating" itemscope itemtype="http://schema.org/AggregateRating">
    <span itemprop="ratingValue">4** stars -
    <span itemprop="reviewCount">255** reviews


```

Le stesse informazioni possono essere rappresentate in formato JSON LD del schema.org:

```xml
<script type="application/ld+json">
    "@content":"http://schema.org",
    "@type":"AggregateRating",
    "ratingValue":"4",
    "reviewCount":"255"
</script>
```

L'esempio seguente mostra dei metadati dal sito Web che fornisce i risultati della ricerca avanzata per l'utente finale:

[ ![](web-markup-images/deeplink01.png "Risultati tramite Markup dati strutturati completi.")](web-markup-images/deeplink01.png)

Apple supporta attualmente i seguenti tipi di schema da schema.org:

 - AggregateRating
 - ImageObject
 - InteractionCount
 - Offerte
 - Organizzazione
 - PriceRange
 - Recipe
 - SearchAction

Per ulteriori informazioni su questi tipi di schema, vedere [schema.org](http://schema.org).

## <a name="providing-actions-with-structured-data"></a>Fornendo azioni con dati strutturati

Tipi specifici di dati strutturati consentirà un risultato della ricerca per essere utilizzabili dall'utente finale. Attualmente sono supportate le seguenti azioni:

 - Comporre il numero di telefono.
 - Recupero della direzione, mappa a un determinato indirizzo.
 - Riproduzione di un file audio o video.

Definizione di un'azione per comporre un numero di telefono, ad esempio, potrebbe essere simile al seguente:

```xml
<div itemscope itemtype="http://schema.org/Organization">
    <span itemprop="telephone">(408) 555-1212**


```

Quando il risultato di ricerca viene presentato all'utente finale, verrà visualizzata un'icona piccola telefono nel risultato. Se l'utente tocca l'icona, verrà chiamato il numero specificato.

Il codice HTML seguente aggiunge un'azione per riprodurre un file audio dai risultati della ricerca:

```xml
<div itemscope itemtype="http://schema.org/AudioObject">
    <span itemprop="contentUrl">http://company.com/appname/greeting.m4a**


```

Infine, il codice HTML seguente aggiunge un'azione per ottenere istruzioni dai risultati della ricerca:

```xml
<div itemscope itemtype="http://schema.org/PostalAddress">
    <span itemprop="streetAddress">1 Infinite Loop**
    <span itemprop="addressLocality">Cupertino**
    <span itemprop="addressRegion">CA**
    <span itemprop="postalCode">95014**


```

Per ulteriori informazioni, vedere Apple [sito per sviluppatori di App ricerca](http://developer.apple.com/ios/search/).



## <a name="related-links"></a>Collegamenti correlati

- [Esempi di iOS 9](https://developer.xamarin.com/samples/ios/iOS9/)
- [iOS 9 per gli sviluppatori](https://developer.apple.com/ios/pre-release/)
- [iOS 9.0](https://developer.apple.com/library/prerelease/ios/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html)
- [Guida per programmatori di ricerca di App](https://developer.apple.com/library/prerelease/ios/documentation/General/Conceptual/AppSearch/index.html#//apple_ref/doc/uid/TP40016308)
