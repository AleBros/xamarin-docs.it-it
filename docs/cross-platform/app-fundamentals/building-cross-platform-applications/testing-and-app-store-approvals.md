---
title: Parte 6 - Test e approvazioni per App Store
description: Questo documento descrive come testare un'applicazione multipiattaforma sul dispositivo, gestire i test case, automatizzare i test, eseguire unit test e usare il processo di invio dell'app.
ms.prod: xamarin
ms.assetid: 46E0578A-7EB9-C105-ABB0-A043E501F36B
author: davidortinau
ms.author: daortin
ms.date: 03/23/2017
ms.openlocfilehash: 07eab1e8229cd6600bb8abdd324cb3eb12a8f886
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73016847"
---
# <a name="part-6---testing-and-app-store-approvals"></a>Parte 6 - Test e approvazioni per App Store

## <a name="testing"></a>Test

Molte app (anche app Android, in alcuni negozi) dovranno passare un processo di approvazione prima della pubblicazione; quindi, i test sono fondamentali per garantire che l'app raggiunga il mercato (solo in caso di successo con i clienti). Il test può assumere diverse forme, dal testing unità a livello di sviluppatore alla gestione del test beta in un'ampia gamma di hardware.

### <a name="test-on-all-platforms"></a>Test su tutte le piattaforme

Esistono differenze minime tra le funzionalità supportate da .NET nei dispositivi Windows Phone, tablet e desktop, nonché le limitazioni di iOS che impediscono la generazione di codice dinamico in tempo reale. È possibile pianificare il test del codice su più piattaforme mentre lo si sviluppa o pianificare il tempo per effettuare il refactoring e aggiornare la parte del modello dell'applicazione alla fine del progetto.

È sempre consigliabile usare il simulatore o l'emulatore per testare più versioni del sistema operativo e anche diverse funzionalità/configurazioni del dispositivo.

È anche necessario testare il numero di dispositivi hardware fisici più diversi possibile.

#### <a name="devices-in-cloud"></a>Dispositivi nel cloud

Il telefono cellulare e l'ecosistema di Tablet crescono continuamente, rendendo impossibile testare il numero sempre maggiore di dispositivi disponibili. Per risolvere questo problema, numerosi servizi offrono la possibilità di controllare in remoto molti dispositivi diversi, in modo che le applicazioni possano essere installate e testate senza dover investire direttamente in molti componenti hardware.

[App Center Test](https://docs.microsoft.com/appcenter/test-cloud/preparing-for-upload/uitest) offre un modo semplice per testare le applicazioni iOS e Android su centinaia di dispositivi diversi.

### <a name="test-management"></a>Gestione test

Quando si esegue il test delle applicazioni all'interno dell'organizzazione o si gestisce un programma beta con utenti esterni, esistono due problemi:

- **Distribuzione** : gestione del processo di provisioning (in particolare per i dispositivi iOS) e aggiornamento delle versioni di software ai tester.
- **Commenti e suggerimenti** : raccolta di informazioni sull'utilizzo dell'applicazione e informazioni dettagliate sugli errori che possono verificarsi.

Sono disponibili numerosi servizi che consentono di risolvere questi problemi, fornendo un'infrastruttura incorporata nell'applicazione per raccogliere e creare report sull'utilizzo e sugli errori, nonché semplificare il processo di provisioning per consentire l'iscrizione e la gestione dei tester e dei relativi dispositivi .

[Visual Studio App Center](/appcenter/) offre una soluzione a questi problemi, che fornisce la distribuzione della versione di test, la segnalazione degli arresti anomali e informazioni sofisticate sull'utilizzo delle applicazioni.

### <a name="test-automation"></a>Automazione di test

Novell [UITest](https://docs.microsoft.com/appcenter/test-cloud/preparing-for-upload/uitest) può essere usato per creare script di test automatizzati dell'interfaccia utente che possono essere eseguiti localmente o caricati in [App Center Test](https://docs.microsoft.com/appcenter/test-cloud/).

## <a name="unit-testing"></a>Testing unità

### <a name="touchunit"></a>Touch. unit

Xamarin.iOS include un Framework di unit test denominato touch. Unit che segue i test di scrittura in stile JUnit/NUnit.

Per informazioni dettagliate sulla scrittura di test e sull'esecuzione di touch. Unit, vedere la documentazione relativa agli [unit test con Xamarin.iOS](~/ios/deploy-test/touch.unit.md) .

### <a name="andrunit"></a>Andr. unit

È disponibile un equivalente open source di touch. Unit per Android denominato Andr. Unit. È possibile scaricarlo da [GitHub](https://github.com/spouliot/Andr.Unit) e leggere lo strumento nel [Blog di@spouliot](https://spouliot.wordpress.com/2011/10/30/andr-unit-joins-the-family/).

## <a name="app-store-approvals"></a>Approvazioni di App Store

Apple e Microsoft gestiscono l'unico archivio sulle piattaforme: App Store e Marketplace rispettivamente. Entrambi bloccano i dispositivi e implementano una rigorosa procedura di revisione dell'app per controllare la qualità delle applicazioni disponibili per il download. La natura aperta di Android significa che sono disponibili numerose opzioni di archiviazione che variano da Google Play, che è ampiamente disponibile e non prevede alcun processo di revisione, ad AppStore di Amazon per Android e a sforzi specifici dell'hardware come le app Samsung con una distribuzione più limitata e implementano un processo di approvazione.

L'attesa della revisione di un'app può essere molto stresssa. le pressioni aziendali spesso significano che le applicazioni vengono inviate per l'approvazione con un margine minimo di errore prima di una data di avvio "mirata". Il processo stesso può richiedere fino a due settimane e non è necessariamente trasparente: è disponibile un feedback limitato sullo stato di avanzamento dell'applicazione fino a quando non viene definitivamente rifiutato o approvato. Il rifiuto può comportare la mancanza di una finestra di marketing di opportunità, soprattutto se si verifica più di una volta e le settimane passano tra la data di avvio originale e la fine dell'approvazione dell'app.

### <a name="be-prepared"></a>Prepararsi

Il primo Consiglio: pianificare l'avvio dell'app in anticipo e ottenere i diritti per un possibile rifiuto e un nuovo invio. Per ogni negozio è necessario creare un account prima di inviare l'app: eseguire questa operazione il prima possibile.
Mentre l'iscrizione a Google Play richiede solo pochi minuti se le tue app sono gratuite, il processo diventa molto più complesso se li vendi ed è necessario fornire informazioni bancarie e fiscali. I processi di Apple e Microsoft sono molto più interessati rispetto a Google, potrebbe essere necessaria una settimana o più per ottenere l'approvazione dell'account, in modo da considerare questo tempo nei piani di lancio.

Dopo che l'account è stato approvato, si è pronti per inviare un'app. Il processo effettivo per inviare le app viene trattato nella documentazione seguente:

- [Pubblicazione nell'App Store iOS di Apple](~/ios/deploy-test/app-distribution/app-store-distribution/publishing-to-the-app-store.md)
- [Preparazione di un'app per Google Play](~/android/deploy-test/publishing/publishing-to-google-play/index.md)
- Gli sviluppatori Windows dovrebbero visitare [Windows Dev Center](https://developer.microsoft.com/windows/windows-apps) per informazioni sull'invio delle proprie app.

Nella parte restante di questa sezione vengono illustrati gli aspetti da prendere in considerazione per assicurarsi che l'app venga approvata senza alcun singhiozzo.

### <a name="quality"></a>Qualità

Sembra ovvio, ma le applicazioni vengono spesso rifiutate perché non soddisfano un certo livello di qualità: dopo tutto, questo è il motivo per cui i negozi curati hanno un processo di approvazione in primo luogo.

Gli arresti anomali sono un motivo comune per il rifiuto. Se è troppo semplice per eseguire l'arresto anomalo dell'app, è possibile che venga rifiutato. La maggior parte degli sviluppatori non inviano le proprie app con la previsione che si arresteranno in modo anomalo, ma spesso. Testare accuratamente l'app prima di inviarla, concentrandosi non solo sull'assicurandosi che tutto funzioni, ma anche che vengano gestiti scenari comuni di errore per dispositivi mobili, ad esempio problemi di rete e vincoli di risorse come la memoria o lo spazio di archiviazione. Usare sia il simulatore che i dispositivi fisici per eseguire il test, indipendentemente dalla modalità di esecuzione del codice in un simulatore, solo un dispositivo può dimostrare le reali prestazioni di un'app. Usa il numero di dispositivi diversi che puoi trovare e integra un team di beta-tester se è possibile: i servizi di terze parti possono aiutare a gestire la distribuzione e il feedback beta.

Tutti i sistemi operativi mobili uccidono un'applicazione che non viene avviata abbastanza rapidamente. L'intervallo di tempo consentito varia, ma in generale le app devono essere reattive in pochi secondi e usare le attività in background per eseguire qualsiasi operazione che richiederebbe più tempo. Le app che impiegano troppo tempo per il caricamento o che non sono sufficientemente reattive per un uso normale verranno rifiutate. Fornire sempre commenti e suggerimenti degli utenti quando si verifica un evento in background oppure l'app si presenta in modo anomalo e ancora una volta, viene rifiutata.

### <a name="check-your-edge-cases"></a>Verificare i casi di Edge

Un trap comune per gli sviluppatori non riesce a risolvere i problemi relativi ai bordi, in particolare quelli che richiedono la riconfigurazione del simulatore o del dispositivo per il test appropriato. Può essere facile dimenticare che non tutti i clienti dovranno "consentire" all'app di accedere alla propria posizione perché, dopo che lo sviluppatore ha accettato la richiesta una volta, non verrà mai visualizzata alcuna richiesta. Le autorizzazioni e l'utilizzo della rete sono focalizzate in modo specifico durante il processo di approvazione, il che significa che una piccola supervisione in queste aree può causare il rifiuto.

L'elenco seguente è un valido punto di partenza per verificare i casi di Edge che potrebbero essere stati persi:

- **I clienti possono ' negare l'accesso ai servizi** , soprattutto in iOS, l'accesso ai dati, ad esempio le informazioni sulla posizione geografica, viene fornito solo se l'utente concede l'autorizzazione all'applicazione. I tester di applicazioni devono reinstallare spesso l'applicazione nello stato iniziale e non consentire le richieste di autorizzazione per assicurarsi che l'applicazione si comportano in modo appropriato. Attivare e disattivare l'autorizzazione per verificare il comportamento corretto quando i clienti cambiano idea.
- **I clienti sono ovunque** : non presupporre che un'app verrà usata solo nella città o nel paese in cui è stata sviluppata. Il codice che funziona con le coordinate GPS, i valori di data e ora e le valute può essere influenzato dalla posizione e dalle impostazioni locali del cliente. Tutte le piattaforme offrono un simulatore che consente di specificare posizioni e impostazioni locali diverse, da usare per testare posizioni in altri emisferi e con impostazioni cultura che formattano date e valute in modo diverso. I valori di latitudine e longitudine possono essere positivi o negativi, il separatore decimale può essere un punto o una virgola e le date possono essere formattate con una miriade di modi.
- **Connessioni di rete lente** : gli sviluppatori di app lavorano spesso in un "mondo ideale" della connettività di rete veloce e sempre funzionante, che ovviamente non è il caso del mondo reale. Il test con una connettività di rete lenta (ad esempio una connessione 3G scadente) e senza l'accesso alla rete è fondamentale per garantire che non venga distribuita un'app bacato. Il processo di approvazione includerà sempre un test con il dispositivo in modalità aereo, quindi assicurarsi che sia stato testato.
- **Hardware varia** : ricordarsi di testare l'hardware più recente e più lento che si prevede di supportare. Esistono due aspetti che potrebbero influire sull'app: le prestazioni, che potrebbero essere inutilizzabili in un dispositivo precedente, e il supporto per le funzionalità hardware quali una fotocamera, un microfono, un GPS, un giroscopio o un altro componente facoltativo. Quando un componente non è disponibile, le applicazioni dovrebbero peggiorare normalmente (e non arrestarsi in modo anomalo).

### <a name="guidelines-are-more-than-just-a-guide"></a>Le linee guida sono più di una semplice ' guida '

Apple è famoso per essere rigoroso rispetto alle linee guida dell'interfaccia umana, in quanto uno dei principali punti di forza della loro piattaforma è la coerenza e l'incremento percepibile di usabilità. Microsoft ha adottato un approccio simile a quello delle applicazioni Windows che implementano il [sistema di progettazione Fluent](https://microsoft.com/design/fluent). Il processo di approvazione per entrambe le piattaforme comporterà la valutazione dell'applicazione per la relativa adesione alla filosofia di progettazione pertinente.

Questo non significa che l'innovazione dell'interfaccia utente non è supportata o non è consigliata, ma ci sono alcuni aspetti da evitare o l'app verrà rifiutata.

In iOS è incluso un uso improprio delle icone predefinite o l'uso di altre metafore ben stabilite in modo non coerente; ad esempio, usando l'icona "compose" per qualsiasi elemento diverso dalla creazione di nuovi contenuti.

Gli sviluppatori Windows dovrebbero prestare attenzione allo stesso modo. un errore comune è la mancata corretta supporto del pulsante hardware indietro in base alle linee guida di Microsoft.

Incoraggiare i progettisti a leggere e seguire le linee guida di progettazione per ogni piattaforma.

### <a name="implementing-platform-specific-features"></a>Implementazione di funzionalità specifiche della piattaforma

Sono molto più rigorosi quando si tratta di implementare servizi specifici della piattaforma, soprattutto in iOS. Per evitare il rifiuto automatico da parte di Apple, è necessario seguire alcune regole con le seguenti funzionalità di iOS:

- **Acquisti in-app** : le applicazioni non devono implementare meccanismi di pagamento esterni per i prodotti digitali, tra cui valuta del gioco, funzionalità dell'applicazione, sottoscrizioni di riviste e molto altro ancora. per questo tipo di funzionalità, le app iOS devono usare il servizio Apple basato su iTunes. Esiste una scappatoia: app come il lettore Kindle e alcune app basate su sottoscrizione consentono di acquistare contenuto altrove che viene collegato a un "account" a cui è possibile accedere tramite l'app, ma in questo caso l'app non deve contenere collegamenti o riferimenti al il processo di acquisto out-of-app (o, anche in questo caso, verrà rifiutato).
- **backup iCloud** : con l'avvento di iCloud, i revisori di Apple sono molto più rigorosi riguardo al modo in cui le app utilizzano l'archiviazione (per garantire che l'esperienza di backup remoto del cliente sia piacevole). Per le app che sprecano spazio di archiviazione che può essere rifiutato, usare la cartella della cache in modo appropriato e seguire le altre linee guida relative all'archiviazione di Apple.
- **Edicola** : le app giornaliere e riviste sono ideali per l'edicola Apple. Tuttavia, le app devono implementare almeno una sottoscrizione di rinnovo automatico e supportare l'approvazione del download in background.
- **Maps** : è sempre più comune aggiungere sovrapposizioni e altre funzionalità alle mappe per dispositivi mobili. Tuttavia, prestare attenzione a non nascondere le informazioni sui crediti della mappa, ad esempio il logo Google in iOS5, in quanto questa operazione comporterà il rifiuto.

### <a name="manage-your-metadata"></a>Gestire i metadati

Oltre ai problemi tecnici ovvi che possono comportare il rifiuto di un'applicazione, esistono alcuni aspetti più sottili dell'invio che potrebbero comportare il rifiuto, in particolare intorno ai metadati (descrizione, parole chiave e immagini di marketing) inviare con l'applicazione per la visualizzazione nell'App Store o nel Marketplace.

- **Immagini** : seguire le linee guida della piattaforma per le icone dell'applicazione e le immagini del negozio. Non usare immagini con marchio, le app sono state rifiutate perché le icone in primo piano sono state disegnate da un iPhone.
- **Marchi** : evitare di usare marchi diversi da quelli personalizzati. Le app sono state negate per citare i marchi nella descrizione dell'app o anche nelle parole chiave nell'App Store di Apple.
- **Descrizione** : non usare la parola ' beta ' o in alcun modo per indicare che l'app non è pronta per la prima volta. Non menzionare altre piattaforme mobili (anche se l'app è multipiattaforma). Soprattutto, assicurarsi che l'app faccia esattamente ciò che si afferma. Se si elenca una serie di funzionalità nella descrizione, è stato meglio ovviare a come usare ognuna di queste funzionalità o si otterrà una "funzionalità indicata nella descrizione dell'applicazione non implementata".

Inserire il maggior lavoro possibile nei metadati dell'applicazione in fase di sviluppo e test. Le applicazioni vengono rifiutate per violazioni di minore entità nei metadati, quindi è opportuno prendere il tempo giusto.

### <a name="app-stores-not-for-everyone"></a>App Store: non per tutti

L'obiettivo principale dei negozi in ogni piattaforma è la distribuzione dei consumatori, ovvero la possibilità di raggiungere il maggior numero possibile di clienti. Tuttavia, non tutte le applicazioni sono destinate agli utenti, c'è una base in rapida crescita di applicazioni interne e Extranet, che richiedono una distribuzione limitata a dipendenti, fornitori o clienti. Queste app non sono "per la vendita" e non richiedono l'approvazione, perché lo sviluppatore controlla la distribuzione a un gruppo di utenti chiuso.
Il supporto per questo tipo di distribuzione varia in base alla piattaforma.

Android offre la massima flessibilità a questo proposito: le applicazioni possono essere installate direttamente da un URL o un allegato di posta elettronica (purché la configurazione del dispositivo lo consenta). Ciò significa che è semplice creare e distribuire applicazioni aziendali interne o pubblicare applicazioni in specifici clienti o fornitori.

Apple fornisce un'opzione di distribuzione interna per gli sviluppatori iscritti al programma iOS Developer Enterprise, che ignora il processo di approvazione dell'app Store e consente alle aziende di distribuire le app interne ai dipendenti.
Sfortunatamente, questa licenza non risolve la necessità di distribuire app simili a Extranet ad altri gruppi chiusi di clienti o fornitori. [Distribuzione Enterprise (e ad hoc)](~/ios/deploy-test/app-distribution/ipa-support.md)

### <a name="app-store-summary"></a>Riepilogo App Store

Il processo di revisione può essere scoraggiante, ma come il resto del ciclo di vita di sviluppo, è possibile garantire il successo con alcune attività di pianificazione e attenzione ai dettagli. Si tratta di una procedura semplice: leggere e comprendere le linee guida dell'interfaccia utente a cui è necessario attenersi, attenersi alle regole se si implementano funzionalità specifiche della piattaforma, testare accuratamente (quindi verificare altre informazioni) e infine verificare i metadati dell'applicazione è corretto prima di inviare.

Un'ultima parola di Consiglio per gli sviluppatori che pubblicano Google Play: la mancanza di un processo di approvazione potrebbe sembrare un processo più semplice, ma i clienti saranno ancora più impegnativi rispetto a un team di revisione. Attenersi a queste linee guida come se l'app venisse rifiutata. in caso contrario, i clienti faranno il rifiuto.
