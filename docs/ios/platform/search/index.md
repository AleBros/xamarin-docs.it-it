---
title: Nuove API di ricerca
description: In questo articolo viene illustrato l'utilizzo di nuove API di ricerca di App di iOS 9 per consentire agli utenti di eseguire la ricerca di informazioni e funzionalità in App xamarin. IOS.
ms.prod: xamarin
ms.assetid: 7323EB3D-A78F-4BF0-9990-3160C7E83CF0
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.openlocfilehash: 5dcd3d9665befaa82fd0f5677a4a662f633ed45b
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2018
---
# <a name="new-search-apis"></a>Nuove API di ricerca

_In questo articolo viene illustrato l'utilizzo di nuove API di ricerca di App di iOS 9 per consentire agli utenti di eseguire la ricerca di informazioni e funzionalità in App xamarin. IOS._

Ricerca è stata espansa in iOS 9 per fornire nuovi modi per accedere alle informazioni e funzionalità all'interno di un'app xamarin. Utilizzando le nuove API di ricerca di App, app contenuto è reso disponibile per la ricerca tramite Spotlight e Safari risultati della ricerca, uniforme e promemoria Siri e suggerimenti. Ciò consente agli utenti di accedere rapidamente alle attività e le informazioni complete all'interno dell'app.

Inoltre, le nuove API di ricerca semplificano l'integrazione di ricerca nell'app senza un'esperienza di implementazione di ricerca precedente. Per questo motivo, le attestazioni Apple richiede in genere alcune ore per rendere il contenuto di un'app di iOS 9 universalmente ricercabili mediante ricerca di App.

[![](images/intro01.png "Un esempio del contenuto di app iOS 9 universalmente ricercabile mediante ricerca di App")](images/intro01.png#lightbox)

Ricerca di App è costituito da tre API diverse:

1. [**NSUserActivity** ](nsuseractivity.md) -si tratta di un'estensione dell'API Handoff Apple rilasciate in iOS 8. Viene utilizzato per eseguire la cronologia di interazione dell'app ricerca sia pubblico e privato) dall'utente.

2. [**Core Spotlight** ](corespotlight.md) -consente a un'app per indicizzarne il contenuto per essere visualizzata nei risultati della ricerca. E funziona come un'API in cui gli elementi possono essere aggiunti e rimossi ed è il modo migliore per contenuto privato dell'indice all'interno di un'applicazione di database.

3. [**WebMarkup** ](web-markup.md) : per le applicazioni che forniscono l'accesso ai contenuti tramite un'interfaccia web (non solo all'interno dell'app). Il contenuto Web può essere contrassegnato con collegamenti speciali che verranno indicizzati da Apple e forniscono il collegamento completo per l'app in dispositivi iOS 9 dell'utente.

## <a name="selecting-an-app-search-approach"></a>Selezione di un approccio di ricerca di App

Prima di decidere quale di questi metodi da implementare dipende i tipi di interazione fornita dall'app e il tipo di contenuto che presenta.

Utilizzare le linee guida seguenti:

- [**NSUserActivity** ](nsuseractivity.md) : utilizzare questo framework per fornire la ricerca per i contenuti pubblici e privati e anche la ricerca di punti di navigazione all'interno dell'app.

- [**Core Spotlight** ](corespotlight.md) : utilizzare questo framework per fornire la ricerca per i dati privati archiviati sul dispositivo.

- [**Web Markup** ](web-markup.md) : utilizzare questo framework per fornire la ricerca per le applicazioni che presentano il loro contenuto non solo all'interno dell'app, ma anche sito Web dell'app.

Ogni della ricerca App approcci sono distinti e possono essere utilizzato singolarmente, tuttavia Apple infatti stati progettati per essere utilizzati insieme. Quando si utilizza più di uno degli approcci disponibili per l'indice di un elemento specifico, assicurarsi di usare lo stesso **ID elemento** su ogni approccio, pertanto singoli collegamenti contemporaneamente.

Utilizzo di più di un approccio non solo garantisce che il contenuto verrà rilevato dall'utente finale, ma consente inoltre di migliorare la classificazione dell'elemento all'interno di ricerca.

Durante il processo di classificazione in estremamente trasparente allo sviluppatore di interazione dell'utente con un determinato elemento un peso molto al momento questa classificazione (ad esempio l'utente toccare un collegamento).
Fornendo elementi informativi RTF, è possibile garantire che un utente verrà essere caso vi riesca per interagire con il contenuto, pertanto la generazione di sua valutazione.

## <a name="what-content-to-index"></a>Il contenuto all'indice

Apple fornisce i suggerimenti seguenti per il contenuto e le azioni per fornire gli indici di ricerca nell'app:

 - Qualsiasi contenuto visualizzato, creata o curata relativa dall'utente all'interno dell'app.
 - Punti di navigazione e funzionalità all'interno dell'app.
 - Elementi come nuovi messaggi, il contenuto o altri tipi di elementi visualizzati dall'app che di recente sono stati scaricati nel dispositivo.

## <a name="app-search-enhancements"></a>Miglioramenti della ricerca di App

Core Spotlight in iOS 10 offre vari miglioramenti per la ricerca di App, ad esempio:

- **Crowdsourced con collegamento diretto popolarità (con privacy differenziale)** -consente di promuovere i contenuti dell'app con collegamento diretto nei risultati della ricerca.
- **La ricerca in-App** -usare il nuovo `CSSearchQuery` classe per fornire capacità di ricerca Spotlight nell'applicazione simile al funzionano delle app di posta elettronica, messaggi e le note.
- **Ricerca di continuazione** : consente di avviare una ricerca Spotlight o Safari, quindi aprire un'app e continuare la ricerca.
- **Visualizzazione dei risultati della convalida** -Apple [lo strumento di convalida API di ricerca di App](https://search.developer.apple.com/appsearch-validation-tool) viene visualizzata una rappresentazione visiva del markup di un sito Web e il collegamento diretto quando preforming test.
- **Messaggio App immagine condivisione** -consente comuni delle immagini nell'applicazione previsti nelle ricerche in evidenza la condivisione in messaggi (tramite un messaggio dell'estensione App).

Per ulteriori dettagli, consultare il nostro [miglioramenti della ricerca di App](~/ios/platform/search/app-search-enhancements.md) Guida.

### <a name="proactive-suggestions"></a>Suggerimenti attiva

iOS 10 presenta nuove modalità della Guida del progetto per un'app per consentire al sistema in modo proattivo presentare informazioni utili automaticamente all'utente in momenti appropriati. Come iOS 9 fornita la possibilità di aggiungere una ricerca approfondita all'app usando Spotlight, uniforme e i suggerimenti Siri con iOS 10 che un'app è possibile esporre funzionalità che possono essere presentate all'utente dal sistema da all'interno delle posizioni seguenti:

- La selezione di App
- Schermata di blocco
- CarPlay
- Mappe
- Interazioni di Siri
- Suggerimenti QuickType 

Un'app espone questa funzionalità per il sistema utilizzando una raccolta di tecnologie, ad esempio [NSUserActivity](https://developer.xamarin.com/api/type/Foundation.NSUserActivity/), markup web, componenti di base Spotlight, MapKit, Media Player e UIKit.

Per ulteriori dettagli, vedere il nostro [suggerimenti proattiva](~/ios/platform/search/proactive-suggestions.md) Guida.

## <a name="summary"></a>Riepilogo

In questo articolo è illustrati le nuove funzionalità dell'API di ricerca che iOS 9 fornisce per le app xamarin. IOS. Si è interessati [NSUserActivity](nsuseractivity.md), [Spotlight Core](corespotlight.md) e [Web Markup](web-markup.md) metodi per l'indicizzazione di contenuto. Completamento con una breve descrizione di quando utilizzare un approccio di ricerca specificato e i tipi di contenuto devono essere indicizzata.



## <a name="related-links"></a>Collegamenti correlati

- [Esempi di iOS 9](https://developer.xamarin.com/samples/ios/iOS9/)
- [iOS 9 per gli sviluppatori](https://developer.apple.com/ios/pre-release/)
- [iOS 9.0](https://developer.apple.com/library/prerelease/ios/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html)
- [Guida per programmatori di ricerca di App](https://developer.apple.com/library/prerelease/ios/documentation/General/Conceptual/AppSearch/index.html#//apple_ref/doc/uid/TP40016308)
