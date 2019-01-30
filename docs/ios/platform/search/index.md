---
title: API di ricerca in xamarin. IOS
description: Questo articolo illustra le nuove API di ricerca di App fornito da iOS 9 di consentire agli utenti di eseguire la ricerca di informazioni e funzionalità nelle App xamarin. IOS.
ms.prod: xamarin
ms.assetid: 7323EB3D-A78F-4BF0-9990-3160C7E83CF0
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/20/2017
ms.openlocfilehash: b2968399279fe3e9d160471bbcae08ae091be93e
ms.sourcegitcommit: a1a58afea68912c79d16a3f64de9a0c1feb2aeb4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/30/2019
ms.locfileid: "55233302"
---
# <a name="search-apis-in-xamarinios"></a>API di ricerca in xamarin. IOS

_Questo articolo illustra l'uso di API di ricerca App fornito da iOS 9 per consentire agli utenti di eseguire la ricerca di informazioni e funzionalità nelle App xamarin. IOS._

Ricerca è stata estesa in iOS 9 per offrire eccezionali nuovi modi per accedere alle informazioni e funzionalità all'interno di un'app xamarin. IOS. Usa le nuove API di ricerca di App, app contenuto venga reso disponibili per la ricerca tramite Spotlight e Safari risultati della ricerca, passaggio di consegne e Siri promemoria e suggerimenti. Ciò consente agli utenti di accedere rapidamente alle attività e le informazioni complete all'interno dell'app.

Inoltre, le nuove API di ricerca rendono più semplice integrare la ricerca nella tua app senza esperienza di implementazione di ricerca precedenti. Per questo motivo, Apple richiede che richiede in genere alcune ore per rendere il contenuto di un'app di iOS 9 universalmente disponibili per la ricerca con ricerca di App.

[![](images/intro01.png "Un esempio del contenuto di app di iOS 9 universalmente disponibili per la ricerca con ricerca di App")](images/intro01.png#lightbox)

Ricerca di App è costituito da tre API diverse:

1. [**NSUserActivity** ](nsuseractivity.md) -questa è un'estensione dell'API Handoff Apple rilasciata in iOS 8. Viene usato per rendere disponibili per la ricerca della cronologia di interazione dell'app sia pubblico e privato) dall'utente.

2. [**Funzionalità di base in evidenza** ](corespotlight.md) -consente a un'app per indicizzarne il contenuto per essere visualizzata nei risultati della ricerca. Funziona come un'API in cui gli elementi possono essere aggiunti e rimossi ed è il modo migliore per contenuto privato dell'indice all'interno di un'app di database.

3. [**WebMarkup** ](web-markup.md) : per le app che forniscono l'accesso al contenuto tramite un'interfaccia web (non solo dall'interno dell'app). Contenuto Web può essere contrassegnato con collegamenti speciali che verranno indicizzati da Apple e forniscono il collegamento completo all'app nel dispositivo iOS 9 dell'utente.

## <a name="selecting-an-app-search-approach"></a>Selezione di un approccio di ricerca nelle App

Decidere quali di questi metodi da implementare dipende dai tipi di interazione forniti dall'app e il tipo di contenuto che presenta.

Usare le linee guida seguenti:

- [**NSUserActivity** ](nsuseractivity.md) : usare questo framework per fornire individuabilità per contenuto pubblico e privato e anche individuabilità dei punti di navigazione all'interno dell'app.

- [**Core Spotlight** ](corespotlight.md) : usare questo framework per fornire la ricerca per i dati privati archiviati nel dispositivo.

- [**Web Markup** ](web-markup.md) : usare questo framework per fornire la ricerca per le app che presentano il loro contenuto non solo dall'interno dell'app, ma dal sito Web dell'app anche.

Ognuno della ricerca di App, gli approcci sono distinti e possono essere usato singolarmente, tuttavia Apple infatti stati progettati per essere utilizzati insieme. Quando si usa più di uno degli approcci per l'indicizzazione di un elemento specifico, assicurarsi di usare lo stesso **ID elemento** su ogni approccio, pertanto, tale persona dei collegamenti tra loro.

Utilizzo di più di un approccio non solo garantisce che il contenuto verrà rilevato dall'utente finale, ma consente anche di migliorare la classificazione dell'elemento all'interno di ricerca.

Durante il processo di classificazione in estremamente trasparente allo sviluppatore dell'interazione dell'utente con un determinato elemento un peso molto al momento questa classificazione (ad esempio l'utente toccando un collegamento).
Fornendo elementi completi e informativi, è possibile garantire che un utente sarà essere caso vi riesca per interagire con i contenuti, pertanto la generazione di relativo rango.

## <a name="what-content-to-index"></a>Il contenuto all'indice

Apple offre i seguenti suggerimenti per quanto riguarda il contenuto e le azioni per fornire gli indici di ricerca nell'app:

 - Qualsiasi contenuto visualizzato, creata o curata dall'utente all'interno dell'app.
 - Punti di navigazione e le funzionalità all'interno dell'app.
 - Elementi quali nuovi messaggi, contenuto o altri tipi di elementi visualizzati dall'app che sono stati scaricati di recente nel dispositivo.

## <a name="app-search-enhancements"></a>Miglioramenti della ricerca di App

Core Spotlight in iOS 10 offre numerosi miglioramenti alla ricerca di App, ad esempio:

- **Popolarità di collegamento diretto di Crowdsourcing (con privacy differenziale)** -fornisce un modo per promuovere il contenuto di app con collegamento diretto nei risultati della ricerca.
- **La ricerca in-App** -usare il nuovo `CSSearchQuery` classe per fornire capacità di ricerca Spotlight in-app simile al funzionamento delle app di posta elettronica, messaggi e note.
- **Eseguire la ricerca continuazione** : consente agli utenti di avviare una ricerca di Spotlight o Safari, quindi aprire un'app e continuare la ricerca.
- **Visualizzazione dei risultati della convalida** -Apple [lo strumento di convalida API di ricerca di App](https://search.developer.apple.com/appsearch-validation-tool) ora visualizza una rappresentazione visiva di markup e il collegamento diretto di un sito Web quando preforming test.
- **Messaggio condivisione immagine delle App** -consente di immagini in-app popolari fornite per la condivisione in messaggi (tramite un'estensione dell'App messaggi) deve essere visualizzato nella ricerca Spotlight.

Per altre informazioni, vedere la [miglioramenti della ricerca App](~/ios/platform/search/app-search-enhancements.md) Guida.

### <a name="proactive-suggestions"></a>Suggerimenti proattivi

iOS 10 offre nuove modalità della Guida di engagement in un'app, consentendo al sistema in modo proattivo presentare informazioni utili automaticamente all'utente in momenti appropriati. Altrettanto iOS 9 è stato possibile aggiungere la ricerca avanzata per l'app usando Spotlight, consegne e suggerimenti di Siri con iOS 10 che un'app può esporre la funzionalità che può essere presentata all'utente dal sistema da all'interno delle posizioni seguenti:

- La selezione di App
- Schermata di blocco
- CarPlay
- Mappe
- Interazioni tra Siri
- Suggerimenti QuickType 

Un'app espone la funzionalità per il sistema utilizzando, ad esempio una raccolta di tecnologie [NSUserActivity](xref:Foundation.NSUserActivity), markup web, Core Spotlight, MapKit, Media Player e UIKit.

Per altre informazioni, vedere la [suggerimenti proattivi](~/ios/platform/search/proactive-suggestions.md) Guida.

## <a name="summary"></a>Riepilogo

Questo articolo ha illustrato la nuova funzionalità dell'API di ricerca che iOS 9 fornisce per le app xamarin. IOS. Viene illustrato [NSUserActivity](nsuseractivity.md), [Core Spotlight](corespotlight.md) e [Markup Web](web-markup.md) metodi per l'indicizzazione dei contenuti. L'operazione terminata con una breve discussione su quando usare un approccio di ricerca specificato e i tipi di contenuto devono essere indicizzati.



## <a name="related-links"></a>Collegamenti correlati

- [Esempi di iOS 9](https://developer.xamarin.com/samples/ios/iOS9/)
- [per gli sviluppatori iOS 9](https://developer.apple.com/ios/pre-release/)
- [iOS 9.0](https://developer.apple.com/library/prerelease/ios/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html)
- [Guida alla programmazione di ricerca nelle App](https://developer.apple.com/library/prerelease/ios/documentation/General/Conceptual/AppSearch/index.html#//apple_ref/doc/uid/TP40016308)
