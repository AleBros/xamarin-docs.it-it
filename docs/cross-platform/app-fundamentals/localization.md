---
title: Localizzazione dell'interfaccia utente dell'applicazione
description: Questo documento vengono descritti i concetti di cross-platform della localizzazione e internazionalizzazione e viene esaminata la modalità in cui influisce sulla progettazione dell'applicazione.
ms.prod: xamarin
ms.assetid: CC6847B2-23FB-4EDE-9F7E-EF29DD46A5C5
author: asb3993
ms.author: amburns
ms.date: 03/22/2017
ms.openlocfilehash: 85e4e98cfbc96936c19642a200885b54cf745aa4
ms.sourcegitcommit: 6be6374664cd96a7d924c2e0c37aeec4adf8be13
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/13/2018
ms.locfileid: "51617514"
---
# <a name="localization"></a>Localizzazione

Questa Guida introduce i concetti di base *internazionalizzazione* e *localizzazione* e collegamenti a istruzioni su come creare applicazioni per dispositivi mobili Xamarin con tali concetti.

Se si vuole passare direttamente i dettagli tecnici di localizzazione delle app di Xamarin, iniziare con uno di questi articoli sulle procedure specifiche della piattaforma:

- [**Xamarin. Forms** ](~/xamarin-forms/app-fundamentals/localization/index.md) localizzazione multipiattaforma usando i file RESX.
- [**Xamarin. IOS** ](~/ios/app-fundamentals/localization/index.md) localizzazione di piattaforma nativa.
- [**Xamarin. Android** ](~/android/app-fundamentals/localization.md) localizzazione di piattaforma nativa.

## <a name="i18n-and-l10n"></a>I18N e L10n

*Internazionalizzazione* è il processo di rendere il codice in grado di visualizzazione di lingue diverse e adattare la visualizzazione per impostazioni locali diverse (ad esempio numero e la formattazione della data). Questo è anche detta *globalizzazione*.

*Localizzazione* è il passaggio seguente: creazione di risorse (ad esempio stringhe e immagini) per ogni lingua e creazione di bundle con l'app internationalize.

Internazionalizzazione che viene spesso abbreviato in i18n: sintassi abbreviata per 18 lettere comprese tra "i" e "n". La localizzazione è passata in modo analogo a L10n – in caso di 10 lettere comprese tra "L" e "n".

## <a name="overview"></a>Panoramica

Questo documento illustra i concetti associati internazionalizzazione e localizzazione e come si applicano in genere allo sviluppo di applicazioni per dispositivi mobili.
Durante la progettazione e creazione di un'applicazione, le cose che in precedenza potrebbe essere hardcoded ma che devono aggiungere parametri per la localizzazione includono:

-   Layout dello schermo e il testo,
-   Le icone, grafica e i colori,
-   File audio e video
-   Testo dinamico e la formattazione del testo (ad esempio numeri, valuta e date),
 - Le modifiche di layout per le lingue di destra a sinistra (RTL), e
-   L'ordinamento dei dati.

Indipendentemente da quali piattaforme per dispositivi mobili, l'app è destinata a questi suggerimenti consentono di creare un'app localizzata di alta qualità.


## <a name="design-considerations"></a>Considerazioni di progettazione

Progettazione di un'applicazione in modo che sia possibile localizzare il contenuto viene chiamato l'internazionalizzazione. Internazionalizzazione correttamente è maggiore del consentendo solo in lingue diverse stringhe da caricare in fase di esecuzione, deve consentire un'applicazione progettata correttamente per tutte le risorse da modificare basati sulla lingua e impostazioni locali (inclusi le immagini, audio e video) e in grado di adattarsi formattazione e layout per far fronte alle diverse dimensioni stringhe.

In questa sezione vengono presentate alcune considerazioni di progettazione da prendere in considerazione quando si compila un'applicazione internazionale.

### <a name="layouts-and-string-length"></a>Layout e la lunghezza della stringa

Cinese e giapponese le stringhe possono essere molto brevi, a volte uno o due caratteri possono essere abbastanza significative per un'etichetta di campo di input.

(Le stringhe in lingua tedesca ad esempio) possono essere molto lunga. In alcuni casi una parola relativamente breve in lingua inglese diventa molto lunga in altri linguaggi, diventando sia troncato o else in modo imprevisto ridisporre il layout.

Confrontare le lunghezze di stringa per alcuni elementi della schermata iniziale iOS in inglese, tedesco e giapponese:

[![](localization-images/language-compare-sml.png "Lunghezza della stringa in lingua giapponese in tedesco di Visual Studio")](localization-images/language-compare.png#lightbox)

Si noti che **impostazioni** in lingua inglese (8 caratteri) richiede 13 caratteri per la traduzione in tedesco, ma solo 2 caratteri in lingua giapponese.

Layout in cui l'etichetta per la visualizzazione e un campo di input sono side-by-side sono difficili da utilizzare quando la lunghezza dell'etichetta può variare notevolmente. Un layout in cui l'etichetta viene visualizzata di sopra di un campo è spesso più facile da localizzare, in quanto l'intera larghezza dello schermo è disponibile per l'etichetta e l'input.

Come regola generale, se si sta compilando i layout fissi (in particolare gli elementi side-by-side) consentono al 50% più larghezza rispetto delle stringhe in inglese richiedono per etichette e il testo. Questo non è stato risolto ogni ma fornirà un buffer che funzionerà in molti casi.

### <a name="input-validation"></a>Convalida dell'input

Prestare attenzione presupposti durante la scrittura di regole di convalida. Potrebbe sembrare valido in modo da richiedere un campo di testo di input "richiedere" almeno tre caratteri in lingua inglese, poiché una singola lettera molto raramente ha alcun significato. In cinese e giapponese tuttavia un singolo carattere potrebbe essere un input valido e una convalida del messaggio "è obbligatorio almeno 3 caratteri" non ha senso per questi linguaggi.

Altre attività apparentemente semplice come la convalida di un indirizzo di posta elettronica o URL del sito Web diventa più complesso con i caratteri non sono limitati al sottoinsieme di ASCII.

Scrivere regole di convalida con internazionalizzazione mente – scegliere le regole meno restrittive o scrivere la logica in modo da utilizzarli in modo diverso per ogni lingua.

### <a name="images-and-color"></a>Colori e immagini

Non tutte le immagini deve cambiare in base alla scelta della lingua dell'utente. Molte delle icone o foto verrà essere adatto per tutti gli utenti, indipendentemente dal linguaggio cui parlano.
Alcune risorse ha senso da localizzare, tuttavia, ad esempio:

 - Le immagini che descrivono gli utenti o percorsi specifici, l'app potrebbe sentirsi più pertinente per gli utenti se viene visualizzato agli utenti e degli indirizzi locali.
 - Icone per alcuni visualizzato può essere specifico delle impostazioni cultura e può semplificare è l'app Usa localizzandone le immagini in modo da riflettere understanding locale.
 - Colori: alcune impostazioni cultura comprensione i colori in modo diverso: rosso potrebbe significare in un'area, ma un'impresa in un altro avviso. Verificare con madrelingua quando si progetta l'app per determinare se si deve essere la creazione di un meccanismo per localizzare i colori.


### <a name="videos-and-sound"></a>Video e audio

Video e audio presente particolari difficoltà durante la localizzazione di un'applicazione, poiché la registrazione di voiceover più rileva anche se è relativamente semplice recuperare le stringhe convertite o clip video può essere costosa sia difficile.

Più copie di file audio e video potrebbero aumentare anche significativamente le dimensioni dell'applicazione (in particolare se si sta eseguendo la localizzazione in un numero elevato di lingue o un numero elevato di file multimediali). È possibile scaricare solo le risorse di lingua richiesta dopo che l'utente ha installato l'app, ma ciò può comportare anche un'esperienza utente misera in reti lente.

Spesso esistono diversi modi per risolvere i problemi di localizzazione: la cosa più importante, è opportuno considerare in anticipo e assicurarsi che l'applicazione è progettata per occuparsi di essi.


### <a name="dates-times-numbers-and-currency"></a>Date, orari, numeri e valuta

Se si usa funzioni di formattazione .NET, ricordarsi di specificare le impostazioni cultura in modo che i separatori decimali vengono analizzati correttamente (ed evitare le eccezioni di conversione che venga generate). Ad esempio 1.99 sia 1,99 sono rappresentazioni decimali valide in base alle impostazioni locali.

Quando i dati provengono da un'origine nota (ie. da codice personalizzato o un servizio web che controllano) è possibile impostare come hardcoded un identificatore delle impostazioni cultura che corrisponde alla formattazione, ad esempio InvariantCulture che funzionerà per la formattazione della lingua inglese standard.

```csharp
double.Parse("1,999.99", CultureInfo.InvariantCulture);
```

Se i dati immesso dall'utente dell'app, analizzarlo usando un'istanza di CultureInfo che riflette le proprie impostazioni internazionali:

```csharp
double.Parse("1 999,99", CultureInfo.CreateSpecificCulture("fr-FR"));
```

Vedere le [l'analisi di stringhe numeriche](http://msdn.microsoft.com/library/xbtzcc4w(v=vs.110).aspx) e [l'analisi di stringhe di data e ora](http://msdn.microsoft.com/library/2h3syy57(v=vs.110).aspx) articoli MSDN per ulteriori informazioni.

<a name="rtl" />

### <a name="right-to-left-rtl-languages"></a>Lingue destra-sinistra (RTL)

Alcuni linguaggi, quali l'arabo, ebraico e Urdu (ad esempio), vengono letti da destra a sinistra.
Le applicazioni che supportano questi linguaggi devono usare le progettazioni di schermata che adattano per lettori di destra a sinistra, ad esempio:

 - Il testo deve essere allineato a destra.
 - Le etichette devono apparire a destra dei campi di input.
 - Posizionamento del pulsante predefinito in genere viene invertito.
 - Navigazione gerarchica scorrendo rapidamente e animazione (e altri metafore di navigazione e le animazioni) che usano direzione per contesto deve anche essere invertito.

IOS e Android supportano layout da destra a sinistra e il rendering del tipo di carattere, con funzionalità incorporate che consentono di apportare le modifiche precedenti. Xamarin. Forms non supporta attualmente automaticamente per il rendering destra-sinistra.

### <a name="sorting"></a>Ordinamento

Lingue diverse definiscono l'ordinamento degli alfabeti in modo diverso, anche quando si usa lo stesso set di caratteri.

Vedere il [dettaglio del confronto tra stringhe](http://msdn.microsoft.com/library/dd465121(v=vs.110).aspx#the_details_of_string_comparison) nelle [procedure consigliate per l'uso di stringhe in .NET Framework](http://msdn.microsoft.com/library/dd465121(v=vs.110).aspx) per un esempio in linguaggio (CultureInfo) influiscono sull'ordinamento.

È improbabile che le funzionalità di database incorporati nelle piattaforme per dispositivi mobili supporterà ordinamento specifiche della lingua di ordinamento in modo che potrebbe essere necessario implementare codice aggiuntivo nella logica di business.

### <a name="text-search"></a>Ricerca di testo

Assicurarsi di aver scritto e testato l'algoritmo di ricerca con più lingue in considerazione. Aspetti da considerare includono:

 - Completamento automatico, se è stata compilata una funzione di completamento automatico assicurarsi che origina i suggerimenti rilevanti per la lingua dell'utente.
 - La query corrispondente ai dati, ovvero eseguirà la ricerca immesse in una determinata query language da eseguire sul contenuto semplicemente scritto in tale linguaggio o su tutto il contenuto nell'app?
 - Stemming – se la ricerca è stata creata per eseguire la ricerca di parole correlate, le radici di word e altre ottimizzazioni di ricerca, sono le ottimizzazioni compilate per tutte le lingue supportate?
 - L'ordinamento: assicurarsi che i risultati vengono ordinati in modo corretto (vedere sopra).


### <a name="data-from-external-sources"></a>Dati da origini esterne

Molte applicazioni il download dei dati da origini esterne, da Twitter e feed RSS meteo, notizie o quotazioni di titoli. Quando si visualizza questo a un utente che è necessario considerare la possibilità che si visualizzerà una schermata di informazioni irrilevanti o non leggibile a essi.

Esistono alcune strategie che è possibile usare per provare e verificare che l'app consente di visualizzare i dati pertinenti per l'utente:

 - Origini diverse: l'applicazione può scaricare i dati da un'origine diversa a seconda della lingua o le impostazioni locali dell'utente. I prezzi di notizie, meteo e stock delle impostazioni locali potrebbero essere più utile rispetto a un elemento scaricato da un feed di America del Nord.
 - Visualizzato localizzato: se si sta visualizzando un Twitter o una foto del feed, si deve visualizzare i metadati (ad esempio, il tempo impiegato) in un proprio linguaggio, anche se il contenuto stesso rimane nella lingua originale.
 - Traduzione – è possibile creare un'opzione di conversione nell'app per eseguire un servizio di traduzione automatica dei dati in ingresso. Ciò potrebbe essere automatica o a discrezione dell'utente, assicurarsi di notificare all'utente se questo è in corso, poiché la traduzione non sono mai perfetta!

Ciò può influire anche su collegamenti esterni alle tracce audio o video: quando si progetta un'applicazione assicurarsi di pianificare in anticipo per generare convertita contenuto o assicurandosi che gli utenti vengono informati in modo adeguato dall'interfaccia utente quando il contenuto non verrà presentato nella loro lingua.


### <a name="dont-over-translate"></a>Risorse in eccesso non tradurre

Alcune stringhe nell'app potrebbero non tradurre minimo necessario oppure particolare attenzione dal convertitore. Alcuni esempi:

 - URL: se si specifica un URL, può o non potrebbe essere necessario affinché venga regolato dal linguaggio. Ad esempio facebook.com non richiede la traduzione automatica-Rileva lingua nella sede principale. Altri siti con contenuto specifico delle impostazioni locali e si potrebbe voler un URL diverso, ad esempio yahoo.com invece yahoo.fr o yahoo.it dell'offerta.
 - Numeri di telefono, soprattutto quelli con i codici paese diversi o i numeri dei chiamanti che parlano una determinata lingua.
 - I dettagli dei contatti, indirizzi e altre informazioni possono variare dalla lingua e impostazioni locali.
 - Marchi e i nomi dei prodotti – alcune stringhe non necessario tradurre perché sono sempre scritto nella stessa lingua.

Infine, assicurarsi di includere le istruzioni dettagliate per la funzione di conversione se determinate stringhe richiedono un trattamento speciale.


### <a name="formatted-text"></a>Testo formattato

Non viene in genere un problema con le App per dispositivi mobili in quanto le stringhe in genere non sono in formato RTF. Tuttavia se è necessario testo RTF (ad esempio, la formattazione in grassetto o corsivo) nell'app assicurarsi la funzione di conversione sa come per la formattazione di input, i file di stringhe archiviano correttamente e sia formattato correttamente prima di essere visualizzato all'utente (ie. non lasciare che accidentalmente i codici di formattazione se stessi essere presentati all'utente).



## <a name="translation-tips"></a>Suggerimenti di traduzione

Traduzione delle stringhe utilizzate da un'applicazione è considerata parte del processo di localizzazione. In genere questa attività verrà assegnate esternamente a un servizio di traduzione ed eseguita da personale multilingua che potrebbe non sapere dell'applicazione o l'azienda.

I suggerimenti seguenti consente di produrre stringhe che sono più facili da convertire in modo accurato e pertanto migliorare la qualità delle App localizzate.



### <a name="localize-complete-strings-not-words"></a>Localizzare stringhe complete, non usare parole

In alcuni casi gli sviluppatori di adottano l'approccio quando si tenta di specificare singole parole o frasi 'frammenti di codice' in modo che è possibile riutilizzarli in tutta l'applicazione. Ad esempio, per il testo "Hai 5 messaggi." si potrebbero specificare le stringhe seguenti per la conversione

**Errata**:

```csharp
"You have"
"no"
"message"
"messages"
```

e quindi provare a creare la frase è corretta su immediatamente nel codice tramite la concatenazione di stringhe:

**Errata**:

```csharp
"You have" + " " + numMsgs + " " + "messages"
"You have" + " no " + "messages"
```

**Questa procedura è sconsigliata** perché non necessariamente funzionerà per tutti i linguaggi e sarà difficile per il translator comprendere il contesto di ogni segmento breve. Tale operazione comporta anche per riutilizzare di stringhe tradotte, questo possono causare problemi in un secondo momento, se vengono usati in contesti diversi (e quindi aggiornati).


### <a name="allow-for-parameter-re-ordering"></a>Attendere un parametro riordinamento delle colonne

Alcuni linguaggi di programmazione richiedono sintassi aggiuntiva per specificare l'ordine dei parametri in una stringa, ma .NET supporta già il concetto di segnaposto numerati, pertanto

**Buona**:

```csharp
"a {0} b {1} cde {3}"
```

potrebbe essere tradotto seguenti (dove la posizione e l'ordine dei segnaposto è stato modificato)

```csharp
"{2} {3} f g h {0}"
```

e i token vengono ordinati come la funzione di conversione previsto. Assicurarsi di includere una spiegazione di cosa contiene ogni segnaposto quando si invia la stringa a una funzione di conversione.


### <a name="use-multiple-strings-for-cardinality"></a>Usare più stringhe per cardinalità

Evitare le stringhe, ad esempio `"You have {0} message/s."` usare stringhe specifiche per ogni stato per offrire un'esperienza utente migliore:

**Buona**:

```csharp
"You have no messages."
"You have 1 messages."
"You have 2 messages."
"You have {0} messages."
```

È necessario scrivere codice nell'app per valutare il numero da visualizzare e scegliere la stringa appropriata. Alcune piattaforme, inclusi iOS e Android, dispongono di caratteristiche incorporate per scegliere automaticamente la stringa al plurale migliore le preferenze per la lingua/impostazioni locali correnti.


### <a name="allowing-for-gender"></a>Consentendo il sesso

I linguaggi basati sull'alfabeto latino talvolta utilizzano parole diverse a seconda del genere del soggetto. Se l'app possa sulla relativa al sesso, è consigliabile consentire le stringhe tradotte riflettere questa situazione.

C'è anche il caso più ovvio anche in inglese, dove le stringhe di fare riferimento a un utente specifico o un utente dell'app. Ad esempio, alcuni siti Visualizza messaggi come `"Bob commented on his post"` pertanto è necessario stringhe per entrambi un sesso maschile, femmina e non binarie o sconosciuto:

**Buona**:

```csharp
"{0} commented on his post"
"{0} commented on her post"
"{0} commented on their post"
```

### <a name="dont-reuse-strings"></a>Non riusare le stringhe

O più precisamente, non riutilizzi stringhe solo perché sono simili quando la stringa ha un diverso scopo o significato.

Ad esempio: si supponga si dispone di un'opzione di attivazione/disattivazione nell'app e il controllo commutatore richiede il testo per 'on' e 'off' deve essere localizzata. È inoltre visualizzare il valore di tale impostazione in un' posizione nell'app in un'etichetta di testo. Si devono usare stringhe diverse per la visualizzazione del commutatore e lo stato dell'opzione (anche se sono la stessa stringa nel linguaggio predefinito), ad esempio:

-   "Sì" – visualizzato sullo stesso commutatore
-   "Off": visualizzato sullo stesso commutatore
-   "Sì", visualizzato in un'etichetta
-   "Off": visualizzati in un'etichetta

Ciò fornisce flessibilità massima per la funzione di conversione:

-   Per motivi legati alla progettazione, ad esempio l'opzione Usa lettere minuscole "on" e "off" ma l'etichetta per la visualizzazione Usa lettere maiuscole "On" e "Off".
-   Alcuni linguaggi potrebbe essere necessario il valore dell'opzione deve essere abbreviato per adattarla a controllo dell'interfaccia utente, mentre la completa parola (tradotta) può essere visualizzato nell'etichetta.
-   In alternativa, per alcune lingue potrebbe essere il rendering del commutatore usano "I" e "O" per acquisire familiarità con relative alla lingua, ma può comunque risultare utile l'etichetta da leggere "On" o "Off".

### <a name="translation-services"></a>Servizi di traduzione

#### <a name="machine-translation"></a>Traduzione automatica

Per integrare le funzionalità di conversione nella tua app, prendere in considerazione la [API traduzione testuale Azure](https://azure.microsoft.com/services/cognitive-services/translator-text-api/).

A scopo di test è possibile utilizzare uno degli molti strumenti di traduzione online per includere testo localizzato nell'app durante lo sviluppo:

- [Bing Translator](https://www.bing.com/translator/)
- [Conversione di Google](http://translate.google.com/)

Esistono molti altri linguaggi disponibili. La qualità della traduzione non è in genere considerata sufficiente per rilasciare un'applicazione senza prima di tutto esaminato e testate da madrelingua o traduttori.

#### <a name="professional-translation"></a>Traduzione professionale

Esistono anche servizi di traduzione professionale che accetta le stringhe e distribuirli ai propri traduttori, grazie a un termine traduzioni a pagamento.

È uno dei servizi più noti commentatori [LionBridge](http://www.lionbridge.com/). Servizi professionali più supportano tutti i tipi di file comuni tra cui stringhe, XML, RESX e TEAPOT/PO.


## <a name="summary"></a>Riepilogo

Questo articolo descrive alcuni dei concetti che è necessario conoscere prima di internazionalizzazione all'app e quindi di localizzare le risorse e anche descritto come modificare le preferenze di lingua per ogni piattaforma.

Questi concetti possono essere applicati per le diverse tecniche specifiche della piattaforma e multi-piattaforma internazionalizzazione sono possibili con Xamarin.

Continuare a leggere i dettagli tecnici per la piattaforma che si è interessati:

- [Xamarin. Forms](~/xamarin-forms/app-fundamentals/localization/index.md) localizzazione multipiattaforma usando i file RESX.
- [Xamarin. IOS](~/ios/app-fundamentals/localization/index.md) localizzazione di piattaforma nativa.
- [Xamarin. Android](~/android/app-fundamentals/localization.md) localizzazione di piattaforma nativa.



## <a name="related-links"></a>Collegamenti correlati

- [Panoramica sulla localizzazione di Apple](https://developer.apple.com/internationalization/)
- [Elenco di controllo per la localizzazione di Android](http://developer.android.com/distribute/tools/localization-checklist.html)
- [Le procedure consigliate per lo sviluppo di applicazioni internazionalizzate (MSDN)](http://msdn.microsoft.com/library/w7x1y988%28v=vs.90%29.aspx)
