---
title: API di ricerca in Novell. iOS
description: Questo articolo illustra l'uso delle nuove API di ricerca di app fornite da iOS 9 per consentire agli utenti di cercare informazioni e funzionalità nelle app Novell. iOS.
ms.prod: xamarin
ms.assetid: 7323EB3D-A78F-4BF0-9990-3160C7E83CF0
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 03/20/2017
ms.openlocfilehash: eb88f7c1de12eee59ea4c2a271079e6b96c29b09
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/04/2019
ms.locfileid: "70286593"
---
# <a name="search-apis-in-xamarinios"></a>API di ricerca in Novell. iOS

_Questo articolo illustra l'uso delle API di ricerca di app fornite da iOS 9 per consentire agli utenti di cercare informazioni e funzionalità nelle app Novell. iOS._

La ricerca è stata espansa in iOS 9 per offrire nuovi modi per accedere alle informazioni e alle funzionalità all'interno di un'app Novell. iOS. Usando le nuove API di ricerca per le app, il contenuto delle app viene reso ricercabile attraverso i risultati della ricerca in evidenza e Safari, i promemoria e i suggerimenti per la consegna e Siri. Ciò consente agli utenti di accedere rapidamente alle attività e alle informazioni in modo approfondito nell'app.

Inoltre, le nuove API di ricerca facilitano l'integrazione della ricerca nell'app senza l'esperienza di implementazione della ricerca precedente. Per questo motivo, Apple dichiara che richiede in genere alcune ore per rendere il contenuto di un'app iOS 9 universalmente ricercabile usando la ricerca di app.

[![](images/intro01.png "Esempio di contenuto dell'app iOS 9 universalmente ricercabile con ricerca app")](images/intro01.png#lightbox)

La ricerca di app è costituita da tre API separate:

1. [**NSUserActivity**](nsuseractivity.md) : si tratta di un'estensione dell'API di consegna rilasciata da Apple in iOS 8. Viene usato per rendere l'utente ricercabile in modo pubblico e privato nella cronologia di interazione delle app.

2. [**Spotlight principale**](corespotlight.md) : consente a un'app di indicizzare il contenuto da presentare nei risultati della ricerca. Funziona come un'API di database in cui è possibile aggiungere e rimuovere elementi ed è il modo migliore per indicizzare contenuto privato all'interno di un'app.

3. [**Webmarkup**](web-markup.md) : per le app che consentono di accedere ai contenuti tramite un'interfaccia Web (non solo dall'interno dell'app). Il contenuto Web può essere contrassegnato con collegamenti speciali che verranno sottoposti a ricerca per indicizzazione da Apple e fornire deep linking all'app nel dispositivo iOS 9 dell'utente.

## <a name="selecting-an-app-search-approach"></a>Selezione di un approccio per la ricerca di app

Decidere quale di questi metodi implementare dipende dai tipi di interazione offerti dall'app e dal tipo di contenuto che presenta.

Usare le linee guida seguenti:

- [**NSUserActivity**](nsuseractivity.md) : usare questo Framework per fornire la ricerca per il contenuto pubblico e privato e anche per la ricerca dei punti di navigazione all'interno dell'app.

- [**Spotlight principale**](corespotlight.md) : usare questo Framework per fornire la ricerca per i dati privati archiviati nel dispositivo.

- [**Markup web**](web-markup.md) : usare questo Framework per fornire la ricerca per le app che presentano il contenuto non solo dall'interno dell'app, ma anche dal sito Web dell'app.

Ognuno degli approcci di ricerca delle app è distinto e può essere usato singolarmente, ma Apple li ha progettati per interagire. Quando si usa più di un approccio per indicizzare un elemento specifico, assicurarsi di usare lo stesso **ID elemento** per ogni approccio, in modo che i singoli collegamenti funzionino insieme.

L'uso di più di un approccio non solo garantisce che il contenuto venga trovato dall'utente finale, ma contribuisce anche a migliorare la classificazione dell'elemento dall'interno della ricerca.

Sebbene il processo di classificazione sia principalmente trasparente per lo sviluppatore, l'interazione dell'utente con un determinato elemento pesa in modo significativo su questo rango, ad esempio l'utente che registra un collegamento.
Fornendo elementi informativi e avanzati, è possibile garantire che un utente possa interagire con il contenuto, aumentando così la pertinenza.

## <a name="what-content-to-index"></a>Contenuto da indicizzare

Apple fornisce i suggerimenti seguenti per quanto riguarda il contenuto e le azioni per fornire gli indici di ricerca nell'app:

- Qualsiasi contenuto visualizzato, creato o curato dall'utente all'interno dell'app.
- Punti di navigazione e funzionalità all'interno dell'app.
- Elementi come nuovi messaggi, contenuto o altri tipi di elementi visualizzati dall'app che sono stati scaricati di recente nel dispositivo.

## <a name="app-search-enhancements"></a>Miglioramenti per la ricerca nelle app

I principali Spotlight in iOS 10 forniscono diversi miglioramenti alla ricerca di app, ad esempio:

- **Popolarità del collegamento di crowdsourcing (con privacy differenziale)** : offre un modo per promuovere il contenuto delle app con collegamento profondo nei risultati della ricerca.
- **Ricerca in-app** : usare la nuova `CSSearchQuery` classe per fornire funzionalità di ricerca Spotlight in-app simili a quelle del funzionamento delle app di posta elettronica, messaggi e note.
- **Continuazione ricerca** : consente a un utente di avviare una ricerca in Spotlight o Safari, quindi aprire un'app e continuare la ricerca.
- **Visualizzazione dei risultati della convalida** : [lo strumento di convalida dell'API ricerca app](https://search.developer.apple.com/appsearch-validation-tool) di Apple ora Visualizza una rappresentazione visiva del markup di un sito Web e il collegamento profondo quando si preformano i test.
- **Condivisione immagini app messaggio** : consente di visualizzare le immagini in-app più diffuse fornite per la condivisione nei messaggi (tramite un'estensione di app per i messaggi) nelle ricerche Spotlight.

Per ulteriori informazioni, vedere la guida ai [miglioramenti](~/ios/platform/search/app-search-enhancements.md) per la ricerca di app.

### <a name="proactive-suggestions"></a>Suggerimenti proattivi

iOS 10 presenta nuovi modi per guidare l'engagement a un'app consentendo al sistema di presentare in modo proattivo le informazioni utili automaticamente all'utente in momenti appropriati. Così come iOS 9 ha fornito la possibilità di aggiungere una ricerca completa all'app usando i suggerimenti Spotlight, Componi e Siri, con iOS 10 un'app può esporre la funzionalità che può essere presentata all'utente dal sistema all'interno delle seguenti posizioni:

- Switcher dell'app
- Schermata di blocco
- CarPlay
- Mappe
- Interazioni Siri
- Suggerimenti QuickType 

Un'app espone questa funzionalità al sistema usando una raccolta di tecnologie come [NSUserActivity](xref:Foundation.NSUserActivity), markup web, Core Spotlight, MapKit, Media Player e UIKit.

Per ulteriori informazioni, vedere la guida ai [suggerimenti proattivi](~/ios/platform/search/proactive-suggestions.md) .

## <a name="summary"></a>Riepilogo

Questo articolo ha trattato le nuove funzionalità dell'API di ricerca disponibili in iOS 9 per le app Novell. iOS. Sono stati analizzati i metodi [NSUserActivity](nsuseractivity.md), [Spotlight principale](corespotlight.md) e [markup web](web-markup.md) per l'indicizzazione del contenuto. Questa operazione è stata completata con una breve discussione su quando è necessario usare un approccio di ricerca specifico e sui tipi di contenuto da indicizzare.



## <a name="related-links"></a>Collegamenti correlati

- [Esempi di iOS 9](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+iOS9)
- [iOS 9 per sviluppatori](https://developer.apple.com/ios/pre-release/)
- [iOS 9.0](https://developer.apple.com/library/prerelease/ios/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html)
- [Guida alla programmazione di app Search](https://developer.apple.com/library/prerelease/ios/documentation/General/Conceptual/AppSearch/index.html#//apple_ref/doc/uid/TP40016308)
