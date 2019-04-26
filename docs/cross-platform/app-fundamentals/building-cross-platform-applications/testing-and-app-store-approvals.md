---
title: Parte 6 - Test e approvazioni per App Store
description: Questo documento descrive come testare un'applicazione multipiattaforma su dispositivo, gestire i test case, automatizza i test, eseguire unit test e di lavoro tramite il processo di invio delle app.
ms.prod: xamarin
ms.assetid: 46E0578A-7EB9-C105-ABB0-A043E501F36B
author: asb3993
ms.author: amburns
ms.date: 03/23/2017
ms.openlocfilehash: 0faf7c9e4ff7c96cdfd25ab6d6658726ef247b32
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61275407"
---
# <a name="part-6---testing-and-app-store-approvals"></a>Parte 6 - Test e approvazioni per App Store

## <a name="testing"></a>Test

Molte App (anche le app Android, in alcuni Store) dovrà superare un processo di approvazione prima che vengano pubblicati; in modo che il test è fondamentale per garantire l'app raggiunge il mercato (tantomeno ha esito positivo con i clienti). Testing può assumere molte forme, dal livello developer di unit test alla gestione dei test della versione beta in un'ampia gamma di hardware.

### <a name="test-on-all-platforms"></a>Esegui test su tutte le piattaforme

Esistono sottili differenze tra gli elementi supportati di .NET su Windows phone, tablet e dispositivi desktop, nonché le limitazioni in iOS che impediscono la dinamica del codice da generare in tempo reale. Il piano per il test del codice su più piattaforme durante lo sviluppo, o pianifica ora effettuare il refactoring e aggiorna il modello-parte dell'applicazione alla fine del progetto.

È sempre consigliabile usare il simulatore/emulatore per eseguire il test più versioni del sistema operativo e anche diverse funzionalità/configurazioni dei dispositivi.

È necessario testare anche sul numero di dispositivi hardware fisici diversi possibili.

#### <a name="devices-in-cloud"></a>Dispositivi nel cloud

L'ecosistema di telefoni e tablet per dispositivi mobili cresce continuamente, rendendo impossibile eseguire il test crescente numero di dispositivi disponibili. Per risolvere questo problema numerosi servizi offrono la possibilità di controllare in remoto i dispositivi diversi, in modo che le applicazioni possono essere installate e testate senza la necessità di investire direttamente in un numero elevato di hardware.

[Test App Center](https://docs.microsoft.com/appcenter/test-cloud/preparing-for-upload/uitest) offre un modo semplice per testare applicazioni iOS e Android in centinaia di dispositivi diversi.

### <a name="test-management"></a>Gestione test

Durante il test delle applicazioni all'interno dell'organizzazione o la gestione di un programma beta con utenti esterni, esistono due sfide:

- **Distribuzione** : gestione del processo di provisioning (soprattutto per i dispositivi iOS) e ottenere le versioni aggiornate del software ai tester.
- **Commenti e suggerimenti** : la raccolta di informazioni sull'utilizzo dell'applicazione e informazioni dettagliate sugli errori che possono verificarsi.


Esistono una serie di servizi aiuto per risolvere questi problemi, fornendo un'infrastruttura che viene compilata nell'applicazione per raccogliere e segnalare errori e utilizzo e anche semplificando il processo di provisioning per consentire per l'abbonamento e gestire i propri dispositivi e i tester .

[Visual Studio App Center](/appcenter/) offre una soluzione a questi problemi, fornendo le informazioni sull'utilizzo di applicazioni sofisticate, segnalazione degli arresti anomali e distribuzione della versione di test.

### <a name="test-automation"></a>Automazione di test

Xamarin [UITest](https://docs.microsoft.com/appcenter/test-cloud/preparing-for-upload/uitest) utilizzabile per creare gli script di test che possono essere eseguiti in locale o caricati in interfaccia utente automatizzati [Test App Center](https://docs.microsoft.com/appcenter/test-cloud/).

## <a name="unit-testing"></a>Testing unità

### <a name="touchunit"></a>Touch

Xamarin. IOS include un framework di unit test denominato Touch che segue lo stile di JUnit/NUnit la scrittura di test.

Fare riferimento a nostro [Unit test con xamarin. IOS](~/ios/deploy-test/touch.unit.md) documentazione per informazioni dettagliate sulla scrittura di test e sull'esecuzione Touch.

### <a name="andrunit"></a>Andr.Unit

È equivalente a touch per Android chiamato Andr.Unit open source. È possibile scaricarlo dal [github](https://github.com/spouliot/Andr.Unit) e continuare a leggere sullo strumento [ @spouliotdel blog](http://spouliot.wordpress.com/2011/10/30/andr-unit-joins-the-family/).

## <a name="app-store-approvals"></a>Approvazioni di App Store

Apple e Microsoft operano nell'archivio solo in piattaforme proposte: l'App Store e un Marketplace rispettivamente. Entrambi bloccare i dispositivi e implementare un processo di revisione app rigorosi per controllare la qualità delle applicazioni disponibili per il download. Natura aperta offre di Android significa che sono disponibili numerose opzioni di archivio che vanno da Google Play, che è ampiamente disponibile e non dispone di alcun processo di revisione, per Appstore di Amazon per Android e specifiche dell'hardware le attività, ad esempio le app Samsung che sono più limitate di distribuzione e implementare un processo di approvazione.

In attesa di un'app per la revisione può essere molto difficile - pressioni business spesso significa che le applicazioni vengono inviate per l'approvazione con il minimo margine di errore prima di una data di avvio "destinazione". Lo stesso processo può richiedere fino a due settimane e non è necessariamente trasparente: è limitato feedback sullo stato di avanzamento dell'applicazione fino a quando non viene infine rifiutato o approvato. Rifiuto può significare manca una finestra del marketing di opportunità, soprattutto se si verifica più volte e passaggio di settimane tra la data di avvio originale e quando l'app viene approvata infine.

### <a name="be-prepared"></a>Essere preparati

La prima parte di consigli: pianificare in anticipo l'avvio dell'app e quindi tenere conto per un rifiuto possibili e nuovo INVIO. Ogni punto vendita è necessario creare un account prima di inviare l'app, eseguire questa operazione non appena possibile.
Durante l'iscrizione a Google Play richiede solo pochi minuti se le app sono gratuite, il processo ottiene molto più complesso se si sono venderli ed è necessario fornire servizi bancari e fiscali. Apple e i processi di Microsoft sono entrambi molto più complessa di Google, potrebbe essere necessaria una settimana o informazioni per ottenere l'account approvato in modo influenzano questa volta i piani di avvio.

Quando l'account è stato approvato, si è pronti per l'invio di un'app. Il processo effettivo per inviare l'App è illustrato nella documentazione seguente:

- [La pubblicazione in iOS di Apple App Store](~/ios/deploy-test/app-distribution/app-store-distribution/publishing-to-the-app-store.md)
- [Preparazione di un'applicazione per Google Play](~/android/deploy-test/publishing/publishing-to-google-play/index.md)
- Gli sviluppatori di Windows, visitare il [Windows Dev Center](https://developer.microsoft.com/windows/windows-apps) a conoscenza di inviare le proprie app.

Il resto di questa sezione vengono illustrati i fattori che è consigliabile prendere in considerazione per assicurarsi che l'app viene approvata senza eventuali interruzioni.

### <a name="quality"></a>Qualità

Sembra ovvio, ma le applicazioni spesso vengono rifiutate perché non soddisfano un certo livello di qualità: dopo tutto, questo è il motivo per cui gli archivi curati hanno un processo di approvazione in primo luogo!

Arresti anomali del sistema sono un motivo comune per il rifiuto. Se è troppo semplice rendere l'arresto anomalo dell'app, è ha garantito a essere rifiutate. La maggior parte degli sviluppatori non inviano le proprie App nella previsione che verrà arrestano, ma spesso lo fanno. Testare accuratamente l'app prima di inviarlo, concentrandosi solo per rendere non che tutto funzioni, ma anche di gestire scenari comuni di errore per dispositivi mobili, ad esempio problemi di rete e i vincoli di risorse, ad esempio memoria o spazio di archiviazione. Usare dispositivi fisici sia il simulatore per testare, indipendentemente dal modo in cui anche codice viene eseguito in un simulatore, solo un dispositivo può dimostrare le prestazioni reali dell'app. Usare diversi dispositivi come è possibile trovare e integrare un team di tester beta Se è possibile, i servizi di terze parti possono aiutare nella gestione di commenti e suggerimenti e la distribuzione beta.

Tutti i sistemi operativi per dispositivi mobili termina un'applicazione che non viene avviato in modo sufficientemente rapido. Il periodo di tempo può varia, ma in generale le app devono tendere a essere reattive in pochi secondi e usare le attività in background per eseguire alcuna operazione che richiederà più tempo. Le app che richiedono troppo tempo per caricare o non risponde in uso normale sei verranno rifiutate. Fornire sempre il feedback degli utenti quando qualcosa che avviene in background o l'app verrà visualizzata per avere un guasto e ancora una volta, vengono rifiutate.

### <a name="check-your-edge-cases"></a>Controllare i casi limite

Trap comune per gli sviluppatori non riesce a indirizzo estreme, specialmente quelle che richiedono riconfigurare il simulatore o il dispositivo per testare in modo corretto. Può essere facile dimenticare che non tutti i clienti sta per "Allow" all'app di accedere ai loro posizione perché dopo che lo sviluppatore ha accettato la richiesta di una sola volta, non verrà mai richiesto nuovamente. Utilizzo di rete e le autorizzazioni sono specificamente sono occupati durante il processo di approvazione, il che significa che una piccola supervisione in queste aree può causare il rifiuto.

Nell'elenco seguente è un buon punto di partenza per il controllo dei casi limite che potrebbero essere stati esclusi:

- **I clienti possono 'deny' accesso ai servizi** , soprattutto in iOS, l'accesso ai dati, ad esempio informazioni di posizione geografica sono disponibile solo se l'utente concede l'autorizzazione all'applicazione. Tester di applicazioni devono spesso installare nuovamente l'applicazione nello stato iniziale e non consentire le richieste di autorizzazione per assicurarsi che l'applicazione si comporta in modo appropriato. Attivare l'autorizzazione e disattivata per verificare il comportamento corretto, come i clienti cambi idea.
- **I clienti sono disponibili ovunque** – non dare per scontato che un'app per essere usata solo nella città o paese in cui è stato sviluppato. Tutti i codice che funziona con le coordinate GPS, i valori di data e ora e valute può essere interessato dalla posizione e le impostazioni locali del cliente. Tutte le piattaforme offerta un simulatore che consentono di specificare percorsi diversi e impostazioni locali - usato per località di test in altri emisferi e con le impostazioni cultura che formattano date e valute in modo diverso. I valori di latitudine e longitudine possono essere positivo o negativo, il separatore decimale può essere un punto o una virgola e le date possono essere formattate una miriade di modi - gestirlo con!
- **Connessioni di rete lente** : consente agli sviluppatori di app spesso lavorano in un mondo' ideale' lavorare veloce, sempre la connettività di rete, che ovviamente non è il caso nel mondo reale. Test con connettività di rete lenta (ad esempio una connessione scarsa 3G) e nessun accesso alla rete è essenziale per garantire che non viene implementata un'app difettoso. Il processo di approvazione verrà sempre includere un test con il dispositivo in modalità aereo, pertanto è necessario verificare che sia stata testata che per se stessi.
- **Hardware varia** : ricordare di test sull'hardware più vecchio, più lenta che si intende supportare. Esistono due aspetti che potrebbero incidere sull'app: prestazioni, che potrebbero essere inutilizzabile in un dispositivo meno recente e il supporto per le funzionalità hardware come una fotocamera, microfono, GPS, Giroscopio o altri componenti facoltativi. Devono ridurre le prestazioni delle applicazioni normalmente (e non di arresto anomalo) quando un componente non è disponibile.

### <a name="guidelines-are-more-than-just-a-guide"></a>Linee guida sono oltre la semplice 'Guida'

Apple è noti per essere severe riguardo la conformità ai loro Human Interface Guidelines come uno dei principali vantaggi della piattaforma è coerenza (e l'aumento percepita di usabilità). Microsoft ha adottato un approccio simile con le applicazioni di Windows che implementa l'interfaccia utente in stile Metro. Il processo di approvazione per entrambe le piattaforme comporterà l'app viene valutato per la conformità alla filosofia di progettazione rilevanti.

Questo non significa che l'innovazione dell'interfaccia utente non è supportato o incoraggiato, ma esistono alcune operazioni che è "semplicemente non deve eseguire" o l'app verrà rifiutato.

In iOS, inclusi uso improprio da parte delle icone predefinite o usando altri metafore consolidati in modo non coerente. ad esempio usando l'icona 'costituiscono' per qualsiasi elemento diverso da creare nuovo contenuto.

Gli sviluppatori di Windows necessario prestare attenzione allo stesso modo; un errore comune non riesce a supportare l'hardware Indietro pulsante in base alle linee guida Microsoft.

Incoraggiare i progettisti per leggere e seguire le linee guida di progettazione per ogni piattaforma.

### <a name="implementing-platform-specific-features"></a>Implementazione di funzionalità specifiche della piattaforma

Le cose sono un po' più severi se si desidera implementare i servizi specifici della piattaforma, in particolare in iOS. Per evitare il rifiuto automatico da Apple, esistono alcune regole da seguire con le seguenti funzionalità di iOS:

- **Acquisti in-App** : le applicazioni non devono implementare meccanismi di pagamento esterno per i prodotti digitali incluse nel gioco valuta, le funzionalità dell'applicazione, sottoscrizioni magazine e molto altro ancora. le app iOS è necessario usare iTunes servizio Apple per questo tipo di funzionalità. È presente un punto debole - App, ad esempio il lettore Kindle e alcune App basate su abbonamento consentono di acquistare il contenuto in un punto che viene associato a un "account" è quindi possibile accedere tramite l'app, tuttavia in questo caso l'app non può contenere collegamenti o riferimenti al out-of-app processo di acquisto (o, ancora una volta, sarà possibile rifiutato).
- **backup in iCloud** – con l'avvento di iCloud, i revisori di Apple sono molto più rigoroso riguardanti come app di usano l'archiviazione (per garantire l'esperienza di backup remoto del cliente è piacevole). Le app che lo spazio di archiviazione in grado di backup bonifica dei rifiuti può ottenere rifiutato, usare quindi la cartella della Cache in modo appropriato e seguire Apple di altre linee guida relative alla memoria.
- **Newsstand** – App riviste e giornali sono un candidato ideale per Newsstand di Apple, ma le app devono implementare almeno un rinnovo automatico sottoscrizione e supporto in background il download per essere approvato.
- **Esegue il mapping** – è sempre più comune per aggiungere mappe per dispositivi mobili sovrapposizioni e altre funzionalità, tuttavia attenzione a non essere per nascondere la mappa 'crediti' informazioni (ad esempio il logo Google in iOS5) come questa operazione comporterà il rifiuto.

### <a name="manage-your-metadata"></a>Gestire i metadati

Oltre agli aspetti tecnici ovvi che possono comportare un'applicazione vengano rifiutata, esistono aspetti ulteriormente meno evidenti dell'invio dell'utente che potrebbe causare il rifiuto, soprattutto per quanto riguarda i metadati (descrizione, parole chiave e le immagini marketing) che si inviare l'applicazione per la visualizzazione all'interno dell'App Store o il Marketplace.

- **Immagini** : seguire le linee guida della piattaforma per le icone dell'applicazione e archiviare le immagini. Non usare immagini di marchio, abbiamo visto le app vengono rifiutate perché le relative icone in primo piano il disegno di un iPhone.
- **Marchi** : evitare di utilizzare qualsiasi marchi diverso dal proprio. Le app sono state negate per citare marchi la descrizione dell'app o persino le parole chiave in Apple App Store.
- **Descrizione** : non usare la parola 'beta' o in qualsiasi modo indicare che l'app non è pronto per il lancio. Non fanno riferimento altre piattaforme per dispositivi mobili (anche se l'app è multipiattaforma). In particolare, assicurarsi che l'app esegue esattamente ciò che viene detto che affermativo. Se si elenca una serie di funzionalità nella descrizione, era meglio sia ovvio come utilizzare ognuna di queste funzionalità o si verificherà un rifiuto "citata nella descrizione dell'applicazione non implementata".

Inserire spendere una fatica eccessiva nei metadati dell'applicazione come nello sviluppo e test. Le applicazioni vengono rifiutate le violazioni secondarie nei metadati in modo che vale la pena grazie alla sfuggito.

### <a name="app-stores-not-for-everyone"></a>App Store: Non per tutti gli utenti

L'obiettivo principale degli archivi in ogni piattaforma è distribuzione consumer - la possibilità di raggiungere clienti tanti possibili. Tuttavia, non tutte le applicazioni destinate al consumer, è presente un'in rapida crescita base delle applicazioni interne e simile a extranet che richiedono la distribuzione limitata per i dipendenti, fornitori o clienti. Queste App non sono "per la vendita" e non richiedono l'approvazione, dopo la distribuzione di controlli per gli sviluppatori a un gruppo di utenti chiuso.
Supporto per questo tipo di distribuzione varia a seconda della piattaforma.

Android offre la massima flessibilità a questo proposito: è possibile installare applicazioni direttamente da un allegato di posta elettronica o URL (purché consente la configurazione del dispositivo). Ciò significa che è estremamente semplice per creare e distribuire applicazioni aziendale interne o pubblicare le applicazioni a clienti specifici o fornitori.

Apple offre un'opzione di distribuzione interna per gli sviluppatori registrati in iOS Developer Enterprise Program, che consente di ignorare il processo di approvazione di App Store e consente alle aziende di distribuire App interne ai propri dipendenti.
Purtroppo questa licenza non soddisfare le esigenze per la distribuzione di app extranet simile ad altri gruppi chiusi di clienti o fornitori. [Enterprise (e Ad Hoc) distribuzione](~/ios/deploy-test/app-distribution/ipa-support.md)

### <a name="app-store-summary"></a>Riepilogo di App Store

Il processo di revisione può essere molto complesso, ma, come il resto del ciclo di vita di sviluppo è possibile garantire successo con una pianificazione e attenzione ai dettagli. Si riduce tutto a pochi semplici passaggi: leggere e comprendere le linee guida dell'interfaccia utente, è necessario rispettare per, conforme alle regole per l'implementazione di funzionalità specifiche della piattaforma, testare accuratamente (quindi alcune informazioni di test) e infine verificare che i metadati dell'applicazione prima di inviare, sia corretto.

Un'ultima parola di consigli per gli sviluppatori di pubblicazione in Google Play: la mancanza di processo di approvazione potrebbe sembrare che semplifica il processo, ma i clienti saranno ancora più complesse rispetto a un team di revisione. Seguire queste linee guida anche se l'app è stato possibile ottenere rifiutato, in caso contrario sarà i clienti che fanno di rifiuto.
