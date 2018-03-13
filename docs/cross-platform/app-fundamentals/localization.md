---
title: Localizzazione di interfaccia utente dell'applicazione
ms.topic: article
ms.prod: xamarin
ms.assetid: CC6847B2-23FB-4EDE-9F7E-EF29DD46A5C5
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.date: 03/22/2017
ms.openlocfilehash: 510e8a6b0b2839a1a191538e7fb4e49bd005b450
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/09/2018
---
# <a name="localization"></a>Localizzazione

Questa guida vengono presentati i concetti di base *internazionali* e *localizzazione* e collegamenti a istruzioni su come creare applicazioni per dispositivi mobili Xamarin con tali concetti.

Se si desidera passare ai dettagli tecnici di localizzazione App Xamarin, iniziare con uno di questi articoli specifici della piattaforma:

- [**Xamarin. Forms** ](~/xamarin-forms/app-fundamentals/localization.md) localizzazione multipiattaforma usando i file RESX.
- [**Xamarin** ](~/ios/app-fundamentals/localization/index.md) localizzazione di piattaforma nativa.
- [**Xamarin** ](~/android/app-fundamentals/localization.md) localizzazione di piattaforma nativa.

## <a name="i18n-and-l10n"></a>I18N e L10n

*Internazionalizzazione* è il processo di rendere il codice in grado di visualizzazione di lingue diverse e l'adattamento la visualizzazione per impostazioni locali diverse (ad esempio numero e la formattazione della data). Questo è anche detta *globalizzazione*.

*Localizzazione* è il passaggio seguente: creazione di risorse (ad esempio le stringhe e immagini) per ogni lingua e la creazione di bundle con l'app internationalize.

Internazionalizzazione che è conosciuta per i18n: sintassi abbreviata per 18 lettere comprese tra "i" e "n". Localizzazione viene ridotto in modo analogo a L10n: per 10 lettere comprese tra "L" e "n".

## <a name="overview"></a>Panoramica

Questo documento introduce i concetti fondamentali di internazionalizzazione e localizzazione e come si applicano in genere per lo sviluppo di applicazioni per dispositivi mobili.
Durante la progettazione e creazione di un'applicazione, operazioni che in precedenza potrebbe essere hardcoded ma che devono con parametri per la localizzazione includono:

-   Layout dello schermo e il testo,
-   Icone, grafica e colori,
-   File audio e video
-   Testo dinamico e la formattazione del testo (ad esempio numeri, valuta e date),
 - Le modifiche di layout per lingue da destra a sinistra (RTL) e
-   Ordinamento dei dati.

Indipendentemente dal fatto che l'app è destinata a questi suggerimenti di piattaforme per dispositivi mobili consentono di creare un'applicazione localizzata di alta qualità.


## <a name="design-considerations"></a>Considerazioni di progettazione

Architettura di un'applicazione in modo che sia possibile localizzare il contenuto, si tratta di internazionalizzazione. Internazionalizzazione correttamente più stringhe di consentendo solo per una lingua diversa da caricare in fase di esecuzione: deve consentire un'applicazione progettata correttamente per tutte le risorse da modificare in base alle lingua e delle impostazioni locali (incluse le immagini, audio e video) e possono essere adattate formattazione e layout per far fronte alle diverse dimensioni stringhe.

In questa sezione vengono illustrate alcune considerazioni di progettazione da prendere in considerazione quando si compila un'applicazione internazionale.

### <a name="layouts-and-string-length"></a>Layout e la lunghezza della stringa

Cinese e giapponese stringhe possono essere molto breve, a volte uno o due caratteri possono essere sufficientemente significativi per un'etichetta di campo di input.

(Tedesche stringhe ad esempio) possono essere molto lunga. In alcuni casi una parola relativamente breve in inglese diventa molto lunga in altri linguaggi: diventando sia troncato o else in modo imprevisto, ridisporre il layout.

Confrontare le lunghezze di stringa per alcuni elementi nella schermata iniziale iOS in inglese, tedesco e giapponese.

[![](localization-images/language-compare-sml.png "Lunghezza della stringa giapponesi in tedesco di Visual Studio")](localization-images/language-compare.png#lightbox)

Si noti che **impostazioni** in lingua inglese (8 caratteri) richiede 13 caratteri per la traduzione in lingua tedesca, ma solo 2 caratteri in giapponese.

Layout in cui l'etichetta di visualizzazione e il campo di input sono side-by-side sono difficili da utilizzare quando la lunghezza dell'etichetta può variare notevolmente. Spesso un layout in cui l'etichetta viene visualizzata di sopra di un campo è più facile da localizzare, in quanto l'intera larghezza dello schermo è disponibile per l'etichetta e l'input.

Come regola generale, se si sta compilando il layout predefinito (in particolare gli elementi side-by-side) consente al 50% richiedono più larghezza delle stringhe in inglese per etichette e il testo. Questo non è stato risolto ogni ma fornirà un buffer che funzionerà in molti casi.

### <a name="input-validation"></a>Convalida dell'input

Fare attenzione dei presupposti durante la scrittura di regole di convalida. Potrebbe sembrare valido in modo da richiedere l'input "require" almeno tre caratteri in inglese, poiché una singola lettera molto raramente ha un significato di un campo di testo. In cinese e giapponese tuttavia un singolo carattere può essere un input valido e una convalida del messaggio "è obbligatorio almeno 3 caratteri" non ha senso per tali lingue.

Altre attività apparentemente semplice come la convalida di un indirizzo di posta elettronica o URL del sito Web diventano più complessi con i caratteri non sono limitati al sottoinsieme di ASCII.

Scrivere le regole di convalida con internazionalizzazione presente: scegliere le regole meno restrittive, oppure scrivere la logica in modo che funziona in modo diverso per ogni lingua.

### <a name="images-and-color"></a>Le immagini e il colore

Non tutte le immagini è necessario modificare in base alle preferenze di lingua di ogni utente. Numero di icone o foto saranno adatto per tutti gli utenti, è importante scegliere la lingua parlano.
Alcune risorse ha senso localizzare, ad esempio:

 - Le immagini che descrivono gli utenti o percorsi specifici: l'app potrebbe percepire più rilevante per gli utenti se viene visualizzato agli utenti e degli indirizzi locali.
 - È possibile rendere più facile da utilizzare per l'immagine di localizzazione in modo da riflettere comprensione locale l'app e icone: alcuni visualizzato possibile specifiche delle impostazioni cultura.
 - Colori: alcune impostazioni cultura comprensione i colori in modo diverso: rosso potrebbe trattarsi di avviso in una regione, ma buona fortuna in un altro. Controllare con madrelingua quando si progetta l'app per determinare se si deve creare un meccanismo per localizzare i colori.


### <a name="videos-and-sound"></a>Video e audio

Video e audio presente particolari problemi durante la localizzazione di un'applicazione, poiché la registrazione più Voice over tiene traccia mentre è relativamente semplice recuperare le stringhe convertite o clip video possono essere difficile e costoso.

Più copie di file audio e video aumentino anche in modo significativo le dimensioni dell'applicazione (in particolare se si sta eseguendo la localizzazione in un numero elevato di lingue o un numero elevato di file multimediali). È possibile scaricare solo le risorse della lingua richiesta dopo che l'utente ha installato l'app, ma ciò potrebbe inoltre causare un'esperienza utente di scarsa qualità su reti lente.

Spesso esistono diversi modi per risolvere i problemi di localizzazione: la cosa più importante è valutarle anticipati e verificare che l'applicazione è progettata per occuparsi di essi.


### <a name="dates-times-numbers-and-currency"></a>Date, ore, numeri e valuta

Se si utilizzano funzioni di formattazione .NET, è necessario specificare le impostazioni cultura in modo che i separatori decimali vengono analizzati correttamente (ed evitare la generazione di eccezioni di conversione). Ad esempio 1.99 sia 1,99 sono rappresentazioni decimali valide in base alle impostazioni locali.

Quando i dati provengano da un'origine conosciuta (ie. da codice personalizzato o un servizio web che è possibile controllare) è possibile impostare come hardcoded un identificatore delle impostazioni cultura con la stessa formattazione, ad esempio l'oggetto InvariantCulture che funziona per la formattazione della lingua inglese standard.

```csharp
double.Parse("1,999.99", CultureInfo.InvariantCulture);
```

Se i dati sono da input dell'utente di app, analizzarlo usando un'istanza di CultureInfo che riflette le proprie impostazioni internazionali:

```csharp
double.Parse("1 999,99", CultureInfo.CreateSpecificCulture("fr-FR"));
```

Vedere il [l'analisi di stringhe numeriche](http://msdn.microsoft.com/en-us/library/xbtzcc4w(v=vs.110).aspx) e [l'analisi di stringhe di data e ora](http://msdn.microsoft.com/en-us/library/2h3syy57(v=vs.110).aspx) articoli MSDN per informazioni aggiuntive.

<a name="rtl" />

### <a name="right-to-left-rtl-languages"></a>Lingue da destra a sinistra (RTL)

Alcuni linguaggi, ad esempio arabi, ebraico e Urdu (ad esempio), vengono letti da destra a sinistra.
Le applicazioni che supportano queste lingue devono utilizzare modelli di schermata che adattano per i reader da destra a sinistra, ad esempio:

 - Testo deve essere allineata a destra.
 - Etichette dovrebbero apparire a destra dei campi di input.
 - Posizionamento del pulsante predefinito in genere viene invertito.
 - Passaggio di navigazione gerarchica e animazione (e altri metafore di navigazione e le animazioni) che utilizzano direzione per il contesto deve inoltre essere invertito.

IOS e Android supportano layout da destra a sinistra e il rendering dei caratteri, con funzionalità incorporate che consentono di apportare le modifiche precedenti. Xamarin. Forms non supporta attualmente automaticamente per il rendering da destra a sinistra.

### <a name="sorting"></a>Ordinamento

Lingue definiscono l'ordinamento degli alfabeti in modo diverso, anche quando usano lo stesso set di caratteri.

Vedere il [dettaglio di confronto tra stringhe](http://msdn.microsoft.com/en-us/library/dd465121(v=vs.110).aspx#the_details_of_string_comparison) in [procedure consigliate per l'uso di stringhe in .NET Framework](http://msdn.microsoft.com/en-us/library/dd465121(v=vs.110).aspx) per un esempio in linguaggio (CultureInfo) influiscono sull'ordinamento.

È improbabile che le funzionalità di database predefiniti disponibili su piattaforme mobili supporteranno ordinamento specifico della lingua di ordinamento in modo che potrebbe essere necessario implementare codice aggiuntivo nella logica di business.

### <a name="text-search"></a>Ricerca di testo

Assicurarsi di scrivere e testare l'algoritmo di ricerca con più lingue presenti. Aspetti da considerare includono:

 - Completamento automatico, se è stata compilata una funzione di completamento automatico assicurarsi che le origini suggerimenti rilevanti per la lingua dell'utente.
 - Query corrispondente ai dati: eseguirà la ricerca query immesse in un determinato linguaggio di essere eseguite solo del contenuto scritto in tale linguaggio o su tutto il contenuto dell'App?
 - Lo stemming: se la ricerca viene compilata per cercare parole simili, radici di word e altre ottimizzazioni della ricerca, è tali ottimizzazioni compilate per tutte le lingue supportate?
 - Ordinamento: assicurarsi che i risultati vengono ordinati correttamente (vedere sopra).


### <a name="data-from-external-sources"></a>Dati da origini esterne

Molte applicazioni scaricare dati da origini esterne da Twitter e feed RSS per i prezzi azionari, notizie o weather. Quando si visualizza a un utente è necessario considerare la possibilità che si visualizza una schermata di informazioni irrilevanti o illeggibili ad essi.

Esistono alcune strategie che è possibile utilizzare per fare in modo che l'app consente di visualizzare dati rilevanti per l'utente:

 - Origini diverse: l'applicazione può scaricare i dati da un'origine diversa a seconda della lingua e impostazioni locali dell'utente. I prezzi di notizie, meteo e stock delle impostazioni locali potrebbero essere più logico di un elemento scaricato da un feed nord americano.
 - Visualizzato localizzato: se si visualizzano un Twitter o foto feed, si deve visualizzare i metadati (ad esempio il tempo impiegato) nel proprio linguaggio, anche se il contenuto rimane nella lingua originale.
 - Traduzione: è possibile creare un'opzione di conversione nell'app per eseguire una traduzione dei dati in arrivo. Può essere automatica o a discrezione dell'utente: appena assicurarsi di notificare all'utente se questa viene eseguita, poiché la traduzione non sono mai perfetta!

Questo potrebbe influire su collegamenti esterni alle tracce audio o video: quando si progetta un'applicazione assicurarsi di pianificare in anticipo per generare convertite contenuto oppure per garantire che gli utenti vengono informati in modo adeguato dall'interfaccia utente quando il contenuto non verrà presentato nella loro lingua.


### <a name="dont-over-translate"></a>Non convertire eccessiva

Alcune stringhe nell'app potrebbero non necessario della conversione, o almeno richiedere un'attenzione speciale dal convertitore. Esempi includono:

 - URL: se si specifica un URL, potrà o non è necessario essere regolato dal linguaggio. Ad esempio, facebook.com non richiede conversione consente di rilevare linguaggio nel sito principale. Contenuto specifico delle impostazioni locali presenti in altri siti e si desidera offrire un URL diverso, ad esempio yahoo.com rispetto yahoo.fr o yahoo.it.
 - Numeri di telefono, in particolare quelli con codici di paese diversi o numeri per i chiamanti che utilizzano una particolare lingua.
 - Dettagli di contatto: indirizzi e altre informazioni possono variare dalla lingua e impostazioni locali.
 - Marchi e i nomi di prodotto: non necessario alcune stringhe di traduzione perché alla scrittura sempre nella stessa lingua.

Infine, assicurarsi di includere le istruzioni dettagliate per la funzione di conversione se alcune stringhe richiedono un trattamento speciale.


### <a name="formatted-text"></a>Testo formattato

Non è in genere un problema con l'App per dispositivi mobili in quanto le stringhe in genere non sono in formato RTF. Tuttavia se RTF (ad esempio la formattazione grassetto o corsivo) è necessaria nell'applicazione verificare il traduttore sa come per la formattazione di input, i file di stringhe archiviano correttamente e sia formattato correttamente prima di essere visualizzato all'utente (ie. non lasciare accidentalmente i codici di formattazione a loro volta essere presentato all'utente).



## <a name="translation-tips"></a>Suggerimenti per la traduzione

Conversione di stringhe utilizzate da un'applicazione è considerata parte del processo di localizzazione. In genere questa attività verrà assegnate esternamente a un servizio di traduzione ed eseguita dal personale multilingua che non è a conoscenza dell'azienda o dall'applicazione.

I suggerimenti seguenti consentono di produrre stringhe che sono più semplici da convertire in modo accurato e pertanto migliorare la qualità delle applicazioni localizzate.



### <a name="localize-complete-strings-not-words"></a>Localizzare stringhe complete, non usare parole

Talvolta gli sviluppatori di adottare l'approccio quando si tenta di specificare singole parole o una frase 'frammenti di codice in modo che è possibile utilizzare nuovamente in tutta l'applicazione. Ad esempio, per il testo "sono presenti 5 messaggi." si potrebbero specificare le stringhe seguenti per la conversione

**Non valido**:

```csharp
"You have"
"no"
"message"
"messages"
```

e quindi si tenta di creare il frase corretto il volo nel codice utilizzando la concatenazione di stringhe:

**Non valido**:

```csharp
"You have" + " " + numMsgs + " " + "messages"
"You have" + " no " + "messages"
```

**Questo è sconsigliato** perché non necessariamente funzionerà per tutti i linguaggi e sarà difficile per la funzione di conversione capire il contesto di ogni segmento breve. Tale operazione comporta anche riutilizzo di stringhe tradotte, questo possono causare problemi in un secondo momento, se vengono usati in contesti diversi (e quindi aggiornate).


### <a name="allow-for-parameter-re-ordering"></a>Consenti per parametro riordinamento delle colonne

Alcuni linguaggi di programmazione richiedono sintassi aggiuntiva per specificare l'ordine dei parametri in una stringa, ma .NET già supporta il concetto di segnaposto numerati, pertanto

**Buona**:

```csharp
"a {0} b {1} cde {3}"
```

Impossibile convertire le operazioni seguenti (dove la posizione e l'ordine dei segnaposto viene modificata)

```csharp
"{2} {3} f g h {0}"
```

e i token verranno ordinati come la funzione di conversione previsto. Assicurarsi di includere una spiegazione di ogni segnaposto contenuto quando si invia la stringa a una funzione di conversione.


### <a name="use-multiple-strings-for-cardinality"></a>Utilizzare più stringhe per cardinalità

Evitare le stringhe come `"You have {0} message/s."` usare stringhe specifiche per ogni stato per fornire una migliore esperienza utente:

**Buona**:

```csharp
"You have no messages."
"You have 1 messages."
"You have 2 messages."
"You have {0} messages."
```

È necessario scrivere codice nell'app per valutare il numero viene visualizzato e scegliere la stringa appropriata. Alcune piattaforme (incluse iOS e Android) includono funzionalità incorporate per scegliere automaticamente la stringa plurale migliore in base alle preferenze per la lingua corrente.


### <a name="allowing-for-gender"></a>Consentendo di sesso

Lingue latine talvolta utilizzano parole diverse in base al sesso del soggetto. Se l'app a conoscenza sesso, è consigliabile consentire le stringhe tradotte in modo da riflettere questo.

È inoltre disponibile il caso più ovvio anche in inglese, dove le stringhe si riferiscono a un utente specifico o un utente dell'app. Ad esempio, alcuni siti mostrano messaggi come `"Bob commented on his post"` pertanto è necessario stringhe per un sesso maschile, femmina e non binari o sconosciuto:

**Buona**:

```csharp
"{0} commented on his post"
"{0} commented on her post"
"{0} commented on their post"
```

### <a name="dont-reuse-strings"></a>Evitare di riutilizzare le stringhe

O, in modo più accurato, evitare di riutilizzare le stringhe solo perché sono simili, quando la stringa ha un diverso scopo o significato.

Ad esempio: supponga disporre di un'opzione di attivazione/disattivazione nell'app e il controllo commutatore richiede il testo per 'on' e 'off' deve essere localizzata. È inoltre visualizzare il valore di tale impostazione altrove nell'app in un'etichetta di testo. Utilizzare stringhe diverse per la visualizzazione del commutatore e lo stato dell'opzione (anche se sono la stessa stringa di lingua predefinita), ad esempio:

-   "On" – visualizzato sullo stesso commutatore
-   "Off": visualizzato sullo stesso commutatore
-   "On" – visualizzati in un'etichetta
-   "Off": visualizzati in un'etichetta

Questo offre la massima flessibilità per la funzione di conversione:

-   Per motivi legati alla progettazione, è forse lo switch stesso utilizza minuscolo "on" e "off" ma l'etichetta di visualizzazione utilizza lettere maiuscole "On" e "Off".
-   Alcune lingue potrebbe essere necessario il valore dell'opzione deve essere abbreviato per adattare il controllo dell'interfaccia utente, mentre la parola completa (tradotta) può essere visualizzato nell'etichetta.
-   In alternativa, per alcune lingue potrebbe essere il rendering del commutatore usare "I" e "O" per conoscenza relative alla lingua, ma è comunque possibile l'etichetta da lettura "On" o "Off".

<!--
# Testing

Once you’ve build and localized your app, you’ll want to be able to test. That means setting your emulator/simulator or device to use another locale or language.

> [!IMPORTANT]
> **WARNING:** Be careful when you set your device to a language you cannot read, as you may not be able to navigate the menu system to return it to your native language!


## iOS

Use Settings.app to switch the language and locale of the iOS Simulator or an iOS device.

On the iOS Simulator you can use the Reset Content and Settings menu item (if the device is in a foreign language and you can’t navigate back to your native tongue).

![]( "ios settings to change language")

## Android

To change the locale on a device

**Home > Menu > Settings > **

Then depending on Android version

**Locale & text > Select locale**

or

**Language & Input > Select language**

![]( "android settings to change language")

When you are testing on the emulator, you can navigate using the settings app as above, or you can reset the locale using the ADB tool command. Using Command Prompt on Windows or Terminal on OS X, start `adb shell` then send commands to set the emulator’s locale. **adb** can usually be found on the Mac in `/Users/YOURNAME/Library/Developer/Xamarin/android-sdk-mac_x86/platform-tools/adb`

### Spanish (Mexico)
setprop persist.sys.language es;setprop persist.sys.country MX;stop;sleep 5;start

### French (France)
setprop persist.sys.language fr;setprop persist.sys.country FR;stop;sleep 5;start

### Japanese (Japan)
setprop persist.sys.language ja;setprop persist.sys.country JP;stop;sleep 5;start

### Portuguese (Brazil)
setprop persist.sys.language pt;setprop persist.sys.country BR;stop;sleep 5;start

### English (USA)
setprop persist.sys.language en;setprop persist.sys.country US;stop;sleep 5;start

**TIP:** the default location of ADB on Mac OS X is
`/Users/[USERNAME]/Library/Developer/Xamarin/android-sdk-mac_x86/platform-tools/adb shell`

-->


### <a name="translation-services"></a>Servizi di traduzione

#### <a name="machine-translation"></a>Traduzione

Per compilare le funzionalità di conversione in un'applicazione, prendere in considerazione il [Azure traduttore testo API](https://azure.microsoft.com/en-au/services/cognitive-services/translator-text-api/).

A scopo di test è possibile utilizzare uno dei numerosi strumenti di traduzione online per includere testo localizzato in app durante lo sviluppo:

- [Bing Translator](https://www.bing.com/translator/)
- [Conversione di Google](http://translate.google.com/)

Esistono molti altri disponibili. La qualità del servizio di traduzione automatica non è in genere considerata adeguata rilasciare un'applicazione senza prima esaminare e testati da traduttori o madrelingua.

 <!--Microsoft's Multilingual Application Toolkit helps you automatically translate strings, and is demonstrated with Xamarin.Forms in [this sample]().-->

#### <a name="professional-translation"></a>Traduzione professionale

Sono inoltre disponibili servizi di traduzione professionali che accetta le stringhe e distribuirli nei propri traduttori, grazie a un termine traduzioni a pagamento.

Uno dei servizi più diffuso è [LionBridge](http://www.lionbridge.com/). Servizi professionali più supportano tutti i tipi di file comuni tra cui stringhe, XML, RESX e TEAPOT/ordine di acquisto.


## <a name="summary"></a>Riepilogo

In questo articolo è stato introdotto alcuni concetti che è necessario conoscere prima di internazionalizzazione app e quindi di localizzare le risorse e inoltre in che modo modificare le preferenze della lingua per ogni piattaforma.

Per le varie tecniche di internazionalizzazione specifico della piattaforma e multipiattaforma che sono possibili con Xamarin, è possono utilizzare questi concetti.

Continuare a leggere i dettagli tecnici per la piattaforma che si è interessati:

- [Xamarin. Forms](~/xamarin-forms/app-fundamentals/localization.md) localizzazione multipiattaforma usando i file RESX.
- [Xamarin](~/ios/app-fundamentals/localization/index.md) localizzazione di piattaforma nativa.
- [Xamarin](~/android/app-fundamentals/localization.md) localizzazione di piattaforma nativa.



## <a name="related-links"></a>Collegamenti correlati

- [Cenni preliminari sulla localizzazione di Apple](https://developer.apple.com/internationalization/)
- [Elenco di controllo per la localizzazione di Android](http://developer.android.com/distribute/tools/localization-checklist.html)
- [Procedure consigliate per lo sviluppo di applicazioni World-Ready (MSDN)](http://msdn.microsoft.com/en-us/library/w7x1y988%28v=vs.90%29.aspx)
