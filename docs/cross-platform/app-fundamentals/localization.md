---
title: Localizzazione dell'interfaccia utente dell'applicazione
description: Questo documento descrive i concetti multipiattaforma di internazionalizzazione e localizzazione ed esamina il modo in cui influisca sulla progettazione dell'applicazione.
ms.prod: xamarin
ms.assetid: CC6847B2-23FB-4EDE-9F7E-EF29DD46A5C5
author: davidortinau
ms.author: daortin
ms.date: 03/22/2017
ms.openlocfilehash: 418c0da2b8fa0e495ae7e6a605c7fde43b6515cb
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73016800"
---
# <a name="localization"></a>Localizzazione

Questa guida presenta i concetti alla base dell' *internazionalizzazione* e della *localizzazione* e fornisce collegamenti alle istruzioni su come produrre applicazioni per dispositivi mobili Xamarin usando questi concetti.

Se si vuole passare direttamente ai dettagli tecnici sulla localizzazione delle app Xamarin, iniziare con uno di questi articoli sulle procedure specifiche della piattaforma:

- Localizzazione multipiattaforma [**Xamarin.Forms**](~/xamarin-forms/app-fundamentals/localization/index.md) con i file resx.
- Localizzazione della piattaforma nativa [**Xamarin.iOS**](~/ios/app-fundamentals/localization/index.md) .
- Localizzazione della piattaforma nativa [**Xamarin.Android**](~/android/app-fundamentals/localization.md) .

## <a name="i18n-and-l10n"></a>i18n e l10n

L' *internazionalizzazione* è il processo che consente di rendere il codice in grado di visualizzare lingue diverse e di adattare la visualizzazione a impostazioni locali diverse, ad esempio la formattazione di numeri e date. Questa operazione viene definita anche *globalizzazione*.

La *localizzazione* è il passaggio che segue: creazione di risorse (ad esempio, stringhe e immagini) per ogni lingua e aggregazione con l'app internazionalizzare.

Internazionalizzazione è spesso abbreviato in i18n, abbreviazione per 18 lettere tra "i" e "n". La localizzazione viene abbreviata in modo analogo a l10n, per 10 lettere tra "L" e "n".

## <a name="overview"></a>Panoramica

Questo documento introduce i concetti associati all'internazionalizzazione e alla localizzazione e al modo in cui si applicano allo sviluppo di applicazioni per dispositivi mobili in generale.
Quando si progetta e si compila un'applicazione, è possibile che in precedenza siano stati impostati come hardcoded, ma che devono essere parametrizzati per la localizzazione:

- Layout dello schermo e testo,
- Icone, grafica e colori,
- File audio e video,
- Testo dinamico e formattazione del testo (ad esempio numeri, valuta e date),
- Modifiche al layout per le lingue da destra a sinistra (RTL) e
- Ordinamento dei dati.

Indipendentemente dalle piattaforme mobili per cui l'app è destinata a questi suggerimenti, sarà possibile creare un'app localizzata di alta qualità.

## <a name="design-considerations"></a>Considerazioni di progettazione

L'architettura di un'applicazione in modo che sia possibile localizzare il contenuto è detta internazionalizzazione. Una corretta internazionalizzazione è più che consentire il caricamento di stringhe di lingua diverse in fase di esecuzione. un'app ben progettata dovrebbe consentire la modifica di tutte le risorse in base alla lingua e alle impostazioni locali (incluse immagini, suoni e video) e può adattarsi formattazione e layout per gestire stringhe di dimensioni diverse.

In questa sezione vengono illustrate alcune considerazioni di progettazione da tenere in considerazione quando si compila un'applicazione internazionalizzata.

### <a name="layouts-and-string-length"></a>Layout e lunghezza di stringa

Le stringhe cinesi e giapponesi possono essere molto brevi, a volte uno o due caratteri possono essere sufficientemente significativi per l'etichetta di un campo di input.

Le stringhe tedesche (ad esempio) possono essere molto lunghe; in alcuni casi una parola relativamente breve in inglese diventa molto lunga in altre lingue, ovvero ritagliata o in altro modo imprevisto.

Confrontare le lunghezze di stringa per alcuni elementi nella schermata iniziale di iOS in inglese, tedesco e giapponese:

[![](localization-images/language-compare-sml.png "German vs Japanese string length")](localization-images/language-compare.png#lightbox)

Si noti che **le impostazioni** in inglese (8 caratteri) richiedono 13 caratteri per la traduzione in tedesco, ma solo 2 caratteri in giapponese.

I layout in cui l'etichetta di visualizzazione e il campo di input sono affiancati sono difficili da usare quando la lunghezza dell'etichetta può variare significativamente. Spesso un layout in cui l'etichetta viene visualizzata sopra un campo è più semplice da localizzare perché la larghezza completa dello schermo è disponibile sia per l'etichetta che per l'input.

Come regola generale, se si compilano layout fissi (in particolare gli elementi affiancati), è possibile utilizzare almeno il 50% di larghezza rispetto alle stringhe inglesi necessarie per le etichette e il testo. Questo non risolverà tutti i problemi, ma fornirà un buffer che funzionerà in molti casi.

### <a name="input-validation"></a>Convalida dell'input

Prestare attenzione ai presupposti per la scrittura delle regole di convalida. Potrebbe sembrare valido richiedere un input di campo di testo per "richiedere" almeno tre caratteri in inglese, perché una singola lettera ha un significato molto raramente. In cinese e giapponese, tuttavia, un singolo carattere può essere un input valido e un messaggio di convalida "almeno 3 caratteri è obbligatorio" non ha senso per tali lingue.

Altre attività apparentemente semplici, ad esempio la convalida di un indirizzo di posta elettronica o di un URL del sito Web, diventano più complesse con i caratteri non limitati al subset ASCII.

Scrivere le regole di convalida con l'internazionalizzazione in mente: scegliere le regole meno restrittive oppure scrivere la logica in modo che funzioni in modo diverso per ogni lingua.

### <a name="images-and-color"></a>Immagini e colore

Non tutte le immagini devono essere modificate in base alla scelta della lingua di un utente. Molte icone o foto saranno adatte a tutti gli utenti, indipendentemente dalla lingua di cui parlano.
Tuttavia, alcune risorse hanno senso localizzare, ad esempio:

- Immagini che illustrano persone o posizioni specifiche: l'app può essere più rilevante per gli utenti se Mostra persone locali/località.
- Icone: alcune iconografiche possono essere specifiche delle impostazioni cultura ed è possibile semplificare l'uso dell'app localizzando le immagini in modo da riflettere le informazioni locali.
- Colori: alcune impostazioni cultura comprendono colori diversi. il rosso potrebbe significare un avviso in un'area, ma buona fortuna in un altro. Verificare con gli speaker nativi quando si progetta l'app per determinare se è necessario creare un meccanismo per localizzare i colori.

### <a name="videos-and-sound"></a>Video e audio

I video e i suoni presentano problematiche particolari durante la localizzazione di un'applicazione, poiché mentre è relativamente semplice ottenere stringhe tradotte, la registrazione di più tracce di VoiceOver o clip video può essere costosa e difficile.

Più copie dei file audio e video possono anche aumentare significativamente le dimensioni dell'applicazione, soprattutto se si sta localizzando in un numero elevato di lingue o se sono presenti molti file multimediali. Si consiglia di scaricare solo le risorse della lingua richieste dopo che l'utente ha installato l'app, ma ciò può anche comportare un'esperienza utente insufficiente in reti lente.

Ci sono spesso diversi modi per risolvere i problemi di localizzazione. la cosa più importante è considerarli in primo piano e assicurarsi che l'applicazione sia progettata per gestirli.

### <a name="dates-times-numbers-and-currency"></a>Date, ore, numeri e valuta

Se si usano le funzioni di formattazione .NET, ricordarsi di specificare le impostazioni cultura in modo che i separatori decimali vengano analizzati correttamente (ed evitare che vengano generate eccezioni di conversione). Ad esempio, sia 1,99 che 1, 99 sono rappresentazioni decimali valide a seconda delle impostazioni locali.

Quando i dati provengono da un'origine nota (ad esempio, dal codice o da un servizio Web che si controlla), è possibile impostare come hardcoded un identificatore di impostazioni cultura che corrisponda alla formattazione, ad esempio InvariantCulture, che funzionerà per la formattazione della lingua inglese standard.

```csharp
double.Parse("1,999.99", CultureInfo.InvariantCulture);
```

Se i dati vengono inseriti dall'utente dell'app, analizzarli usando un'istanza CultureInfo che riflette le impostazioni locali:

```csharp
double.Parse("1 999,99", CultureInfo.CreateSpecificCulture("fr-FR"));
```

Per ulteriori informazioni, vedere gli articoli relativi all' [analisi di stringhe numeriche](https://msdn.microsoft.com/library/xbtzcc4w(v=vs.110).aspx) e di analisi di stringhe di [data e ora](https://msdn.microsoft.com/library/2h3syy57(v=vs.110).aspx) MSDN.

<a name="rtl" />

### <a name="right-to-left-rtl-languages"></a>Lingue da destra a sinistra (RTL)

Alcune lingue, ad esempio l'arabo, l'ebraico e l'Urdu (ad esempio), vengono lette da destra a sinistra.
Le applicazioni che supportano questi linguaggi devono usare le progettazioni dello schermo che si adattano ai lettori da destra a sinistra, ad esempio:

- Il testo deve essere allineato a destra.
- Le etichette devono essere visualizzate a destra dei campi di input.
- La posizione predefinita del pulsante è in genere invertita.
- La navigazione gerarchica e l'animazione (e altre metafore di navigazione e animazioni) che usano la direzione per il contesto dovrebbero anche essere invertite.

IOS e Android supportano layout da destra a sinistra e rendering dei tipi di carattere, con funzionalità predefinite che consentono di apportare le regolazioni precedenti. Xamarin.Forms attualmente non supporta automaticamente il rendering RTL.

### <a name="sorting"></a>Ordinamento

Lingue diverse definiscono il tipo di ordinamento degli alfabeti in modo diverso, anche quando utilizzano lo stesso set di caratteri.

Vedere i [Dettagli del confronto](https://msdn.microsoft.com/library/dd465121(v=vs.110).aspx#the_details_of_string_comparison) tra stringhe nelle [procedure consigliate per l'uso delle stringhe nel .NET Framework](https://msdn.microsoft.com/library/dd465121(v=vs.110).aspx) per un esempio in cui Language (CultureInfo) influisca sull'ordinamento.

È improbabile che le funzionalità predefinite del database sulle piattaforme per dispositivi mobili supportino l'ordinamento specifico della lingua, quindi potrebbe essere necessario implementare codice aggiuntivo nella logica di business.

### <a name="text-search"></a>Ricerca di testo

Assicurarsi di scrivere e testare l'algoritmo di ricerca tenendo presenti più lingue. Di seguito sono riportate alcune considerazioni:

- Completamento automatico: se è stata compilata una funzione di completamento automatico, assicurarsi che origini suggerimenti pertinenti per la lingua dell'utente.
- Corrispondenza tra query e dati: le query immesse in una lingua specifica verranno eseguite solo sul contenuto scritto in tale lingua o su tutto il contenuto dell'app?
- Stemming: se la ricerca è stata compilata per la ricerca di parole simili, radici di parole e altre ottimizzazioni di ricerca, queste ottimizzazioni sono compilate per tutti i linguaggi supportati?
- Ordinamento: assicurarsi che i risultati siano ordinati correttamente (vedere sopra).

### <a name="data-from-external-sources"></a>Dati da origini esterne

Molte applicazioni scaricano i dati da origini esterne, da Twitter e feed RSS a Meteo, notizie o prezzi azionari. Quando viene visualizzato a un utente, è necessario considerare la possibilità che venga visualizzata una schermata di informazioni irrilevanti o illeggibili.

Esistono alcune strategie che è possibile usare per provare a verificare che l'app visualizzi i dati rilevanti per l'utente:

- Origini diverse: l'applicazione potrebbe scaricare i dati da un'origine diversa a seconda della lingua o delle impostazioni locali dell'utente. Le informazioni sulle impostazioni locali, il prezzo meteorologico e quello azionario potrebbero essere più sensate di quelle scaricate da un feed nordamericano.
- Visualizzazione localizzata: se si visualizza un feed Twitter o Photo, è necessario visualizzare i metadati (ad esempio il tempo impiegato) nella propria lingua, anche se il contenuto rimane nella lingua originale.
- Traduzione: è possibile creare un'opzione di conversione nell'app per eseguire una traduzione automatica dei dati in arrivo. Questo potrebbe essere automatico o a discrezione dell'utente. è sufficiente assicurarsi di inviare una notifica all'utente in caso di avvenuta esecuzione, perché le traduzioni dei computer non sono mai perfette.

Questo può anche influire sui collegamenti esterni a tracce audio o video: quando si progetta l'applicazione, assicurarsi di pianificare in anticipo il contenuto tradotto per l'approvvigionamento o garantire che gli utenti siano informati adeguatamente dall'interfaccia utente quando il contenuto non verrà presentato nei linguaggio.

### <a name="dont-over-translate"></a>Non superare la traduzione

È possibile che alcune stringhe nell'app non debbano essere trascinate o che debbano almeno richiedere particolare attenzione da parte del traduttore. Gli esempi possono includere:

- URL: se si elenca un URL, è possibile che non sia necessario modificarlo in base alla lingua. Ad esempio, facebook.com non richiede la traduzione automatica rileva la lingua nel sito principale. Altri siti hanno contenuto specifico delle impostazioni locali ed è consigliabile offrire un URL diverso, ad esempio yahoo.com rispetto a yahoo.fr o yahoo.it.
- Numeri di telefono, soprattutto quelli con codici paese o numeri diversi per i chiamanti che parlano una determinata lingua.
- Dettagli contatto: indirizzi e altre informazioni possono variare in base alla lingua o alle impostazioni locali.
- Marchi & nomi di prodotto: alcune stringhe non necessitano di traduzione perché sono sempre scritte nella stessa lingua.

Infine, assicurarsi di includere istruzioni dettagliate per il traduttore se determinate stringhe richiedono un trattamento speciale.

### <a name="formatted-text"></a>Testo formattato

In genere non è un problema con le app per dispositivi mobili perché le stringhe non sono in genere formattate. Tuttavia, se nell'app è necessario un testo RTF, ad esempio la formattazione in grassetto o in corsivo, assicurarsi che il traduttore sappia come inserire la formattazione, i file di stringhe lo archivino correttamente e formattati correttamente prima di essere visualizzati all'utente (ad esempio, non consentire accidentalmente i codici di formattazione vengono presentati all'utente.

## <a name="translation-tips"></a>Suggerimenti per la traduzione

La conversione delle stringhe utilizzate da un'applicazione viene considerata parte del processo di localizzazione. Questa attività viene in genere esternalizzata a un servizio di traduzione ed eseguita da un personale multilingue che potrebbe non essere a conoscenza dell'applicazione o dell'azienda.

I suggerimenti seguenti consentono di produrre stringhe più facili da tradurre in modo accurato e pertanto di migliorare la qualità delle app localizzate.

### <a name="localize-complete-strings-not-words"></a>Localizzare stringhe complete e non parole

A volte gli sviluppatori hanno l'obiettivo di specificare parole singole o frasi, in modo da poterle riutilizzare nell'intera applicazione. Ad esempio, per il testo "si hanno 5 messaggi". potrebbero specificare le seguenti stringhe per la conversione

Non **valido**:

```csharp
"You have"
"no"
"message"
"messages"
```

quindi provare a creare rapidamente la frase corretta nel codice usando la concatenazione di stringhe:

Non **valido**:

```csharp
"You have" + " " + numMsgs + " " + "messages"
"You have" + " no " + "messages"
```

**Questa operazione è sconsigliata** perché non funzionerà necessariamente per tutti i linguaggi e sarà difficile per il traduttore comprendere il contesto di ogni segmento breve. Comporta anche il riutilizzo delle stringhe tradotte, che possono causare problemi in un secondo momento se vengono usate in contesti diversi (e quindi vengono aggiornate).

### <a name="allow-for-parameter-re-ordering"></a>Consenti nuovo ordinamento dei parametri

Alcuni linguaggi di programmazione richiedono sintassi aggiuntiva per specificare l'ordine dei parametri in una stringa, tuttavia .NET supporta già il concetto di segnaposto numerati.

**Valido**:

```csharp
"a {0} b {1} cde {3}"
```

potrebbe essere tradotto quanto segue (dove la posizione e l'ordine dei segnaposto vengono modificati)

```csharp
"{2} {3} f g h {0}"
```

e i token verranno ordinati come destinazione del traduttore. Assicurarsi di includere una spiegazione del contenuto di ogni segnaposto quando si invia la stringa a un convertitore.

### <a name="use-multiple-strings-for-cardinality"></a>Usare più stringhe per la cardinalità

Evitare stringhe come `"You have {0} message/s."` usare stringhe specifiche per ogni stato per offrire una migliore esperienza utente:

**Valido**:

```csharp
"You have no messages."
"You have 1 message."
"You have 2 messages."
"You have {0} messages."
```

Sarà necessario scrivere il codice nell'app per valutare il numero visualizzato e scegliere la stringa appropriata. Alcune piattaforme, tra cui iOS e Android, includono funzionalità predefinite per scegliere automaticamente la stringa plurale più appropriata in base alle preferenze per la lingua o le impostazioni locali correnti.

### <a name="allowing-for-gender"></a>Consentire il sesso

Le lingue basate su Latin a volte usano parole diverse a seconda del sesso dell'oggetto. Se l'app è a conoscenza del sesso, è necessario consentire alle stringhe tradotte di riflettere questo problema.

Esiste anche il caso più ovvio anche in inglese, in cui le stringhe si riferiscono a una persona o a un utente specifico dell'app. Alcuni siti, ad esempio, visualizzano messaggi come `"Bob commented on his post"` pertanto è necessario disporre di stringhe sia per sesso maschile, femminile che non binario o sconosciuto:

**Valido**:

```csharp
"{0} commented on his post"
"{0} commented on her post"
"{0} commented on their post"
```

### <a name="dont-reuse-strings"></a>Non riutilizzare le stringhe

O più accuratamente, non riutilizzare le stringhe solo perché sono simili quando la stringa ha uno scopo o un significato diverso.

Si supponga, ad esempio, di avere un'opzione di attivazione/disattivazione nell'app e che il controllo switch richieda la localizzazione del testo per "on" e "off". Viene anche visualizzato il valore di tale impostazione altrove nell'app in un'etichetta di testo. È consigliabile usare stringhe diverse per la visualizzazione dello switch rispetto allo stato dell'interruttore (anche se sono la stessa stringa nella lingua predefinita), ad esempio:

- "On": visualizzato sullo switch stesso
- "Off": visualizzato sullo switch stesso
- "On": visualizzato in un'etichetta
- "Off": visualizzato in un'etichetta

Questo garantisce la massima flessibilità per il traduttore:

- Per motivi di progettazione, probabilmente il commutivo usa il minuscolo "on" e "off", ma l'etichetta di visualizzazione usa lettere maiuscole "on" e "off".
- È possibile che alcuni linguaggi debbano abbreviare il valore del commutatore per adattarsi al controllo dell'interfaccia utente, mentre la parola completa (tradotta) può essere visualizzata nell'etichetta.
- In alternativa, per alcune lingue il rendering del Commuter potrebbe essere utilizzato "I" e "O" per conoscere la cultura, ma è comunque possibile che l'etichetta legga "on" o "off".

### <a name="translation-services"></a>Servizi di traduzione

#### <a name="machine-translation"></a>Traduzione automatica

Per creare funzionalità di traduzione nell'app, prendere in considerazione il [API traduzione testuale di Azure](https://azure.microsoft.com/services/cognitive-services/translator-text-api/).

A scopo di test è possibile usare uno dei numerosi strumenti di traduzione online per includere il testo localizzato nell'app durante lo sviluppo:

- [Bing Translator](https://www.bing.com/translator/)
- [Google translate](https://translate.google.com/)

Sono disponibili molte altre. La qualità della traduzione automatica non è generalmente considerata sufficiente per rilasciare un'applicazione senza prima essere esaminata e testata da traduttori professionali o madrelingua.

#### <a name="professional-translation"></a>Traduzione professionale

Ci sono anche servizi di traduzione professionale che porteranno le tue stringhe e le distribuiranno ai loro traduttori, fornendo le traduzioni complete a pagamento.

Uno dei servizi più noti è [Lionbridge](https://www.lionbridge.com/). La maggior parte dei servizi professionali supporta tutti i tipi di file comuni, tra cui stringhe, XML, RESX e POT/PO.

## <a name="summary"></a>Riepilogo

In questo articolo sono stati introdotti alcuni dei concetti che è necessario conoscere prima di internazionalizzazione l'app e quindi la localizzazione delle risorse e viene anche illustrato come modificare le preferenze della lingua per ogni piattaforma.

Questi concetti possono essere applicati alle varie tecniche di internazionalizzazione specifiche della piattaforma e tra piattaforme possibili con Xamarin.

Continua a leggere i dettagli tecnici per la piattaforma a cui sei interessato:

- Localizzazione multipiattaforma [Xamarin.Forms](~/xamarin-forms/app-fundamentals/localization/index.md) con i file resx.
- Localizzazione della piattaforma nativa [Xamarin.iOS](~/ios/app-fundamentals/localization/index.md) .
- Localizzazione della piattaforma nativa [Xamarin.Android](~/android/app-fundamentals/localization.md) .

## <a name="related-links"></a>Collegamenti correlati

- [Panoramica della localizzazione di Apple](https://developer.apple.com/internationalization/)
- [Elenco di controllo della localizzazione di Android](https://developer.android.com/distribute/tools/localization-checklist.html)
- [Procedure consigliate per lo sviluppo di applicazioni internazionali (MSDN)](https://msdn.microsoft.com/library/w7x1y988%28v=vs.90%29.aspx)
