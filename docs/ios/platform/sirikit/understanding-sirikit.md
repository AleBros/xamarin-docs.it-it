---
title: Informazioni sui concetti di SiriKit
description: Questo documento descrive i concetti principali necessari per l'utilizzo di SiriKit in un'app xamarin. IOS. Ad esempio, vengono illustrati gli Intent e le estensioni dell'interfaccia utente Intent, SiriKit autorizzazioni, progettare un'esperienza ottimale e altro ancora.
ms.prod: xamarin
ms.assetid: 99EC5C1E-484F-4371-8555-58C9F60DE37F
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 05/02/2017
ms.openlocfilehash: b72246968c9b321329e56fd51eaaa98a1625922e
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2018
ms.locfileid: "50123076"
---
# <a name="understanding-sirikit-concepts"></a>Informazioni sui concetti di SiriKit

_Questo articolo illustra i concetti principali che saranno necessari per l'utilizzo di SiriKit in un'app xamarin. IOS._


Nuovo ai dispositivi iOS 10, SiriKit consente a un'app xamarin. IOS fornire servizi accessibili all'utente tramite Siri e all'app mappe su un dispositivo iOS. Questa funzionalità viene fornita in uno o più l'estensione dell'App usando le nuove **Intent** e **dell'interfaccia utente Intent** Framework.

SiriKit consente a un'app per iOS fornire servizi accessibili all'utente tramite Siri e all'app mappe su un dispositivo iOS mediante le estensioni App e le nuove **Intent** e **dell'interfaccia utente Intent** Framework.

Siri funziona con il concetto di **domini**, gruppi di conoscono le azioni per le attività correlate. Ogni interazione con l'app con Siri deve rientrare in uno dei domini del servizio noto come indicato di seguito:

- Audio o video chiamata.
- Una questione di prenotazione.
- La gestione di allenamenti.
- Messaggistica.
- Ricerca di foto.
- Inviare o ricevere i pagamenti.

Quando l'utente effettua una richiesta di Siri con uno dei servizi dell'estensione App, SiriKit invia l'estensione di un **finalità** oggetto che descrive la richiesta dell'utente insieme a eventuali dati di supporto. l'estensione dell'App, quindi genera appropriato **risposta** dell'oggetto per il determinato **finalità**, che riporta in dettaglio come l'estensione può gestire la richiesta.

## <a name="the-intents-and-intents-ui-extensions"></a>La finalità e le estensioni dell'interfaccia utente Intent

Siri sia all'app mappe interagire con i servizi dell'app tramite due diversi tipi di estensioni di App:

- **Estensione per Intent** -fornisce Siri e mappe con l'app del contenuto ed esegue le attività necessarie per soddisfare qualsiasi Intent supportati.
- **Estensione per interfaccia utente Intent** -fornisce un'interfaccia utente personalizzata che verrà visualizzata per il contenuto dell'app all'interno di Siri o mappe.

L'app deve fornire un'estensione per Intent per supportare SiriKit ed è responsabile della fornendo informazioni che Siri e mappe possono presentare all'utente e per la gestione di Intent.

Creazione di un'estensione per interfaccia utente Intent è facoltativa poiché Siri in genere gestisce ogni interazione dell'utente e ha un'interfaccia utente standard e incorporata per presentare le informazioni in ognuno dei domini supportati. Fornendo un'estensione per interfaccia utente Intent, l'app può usare la **dell'interfaccia utente Intent** framework per presentare un avanzate e personalizzate dell'interfaccia utente dotata di informazioni personalizzate distintive dell'app e informazioni aggiuntive.

## <a name="siri-and-the-maps-app-role"></a>Siri e il ruolo dell'App mappe

Le richieste vocali dell'utente sono elaborato e analizzato semanticamente da Siri che trasforma le richieste in Intent interattivi in grado di gestire le estensioni con finalità di una lingua.

Maps Usa le estensioni dell'app con finalità per visualizzare le informazioni nell'interfaccia della mappa in risposta alle azioni dell'utente. Ad esempio richiedendo nelle vicinanze ristoranti o recupero ristorante dell'app per le verifiche.

Siri sia mappe gestire tutte le interazioni dell'utente e visualizzare i risultati usando l'interfaccia di sistema standard. Il ruolo di estensioni di app è principalmente per fornire i dati che viene visualizzati. Facoltativamente, l'app può fornire un'estensione per interfaccia utente Intent e presentare un'interfaccia utente personalizzata per migliorare l'interfaccia di sistema predefinito.

## <a name="interacting-with-siri-via-sirikit"></a>L'interazione con Siri tramite SiriKit

Questa sezione viene fornita una panoramica del modo in cui SiriKit consente all'utente di interagire con l'app usando Siri. Ai fini di questo esempio verrà usato l'app MonkeyChat fittizio:

[![](understanding-sirikit-images/monkeychat01.png "L'icona MonkeyChat")](understanding-sirikit-images/monkeychat01.png#lightbox)

MonkeyChat mantiene il proprio contatto libro di amici dell'utente, ognuno associato a un nome della schermata (ad esempio Bobo, ad esempio) e consente all'utente di inviare testo chat a ogni amico in base al nome della schermata.

Esistono molti modi che l'utente può avviare un'interazione con l'app, poiché utenti diversi potrebbero apportare la stessa richiesta in formati diversi.

Ad esempio, se l'utente vuole inviare un messaggio a loro friend Bobo, potrebbe avere la conversazione con Siri seguente:

_Utente: Salve Siri, inviare un messaggio MonkeyChat._<br />
_Siri: A chi?_<br />
_Utente: Bobo._<br />
_Siri: Ciò che si desidera comunicare al Bobo?_<br />
_Utente: Inviare ulteriori Bananas._<br />

Un'altra persona potrebbe apportare la stessa richiesta con una conversazione diversa:

_Utente: Inviare un messaggio a Bobo su MonkeyChat._<br />
_Siri: Ciò che si desidera comunicare al Bobo?_<br />
_Utente: Inviare ulteriori bananas._<br />

E un altro utente potrebbe eseguire una richiesta ancora più breve:

_Utente: MonkeyChat Bobo, inviare più bananas._<br />
_Siri: Ok, l'invio di messaggi inviare ulteriori bananas al Bobo su Monkeychat._<br />

O anche apportare la stessa richiesta in una lingua diversa:

_Utente: MonkeyChat Bobo s'il vous plaît envoyer plus de bananes._<br />
_Siri: Oui, envoyer plaît di envoi messaggio s'il vous più de bananes à Bobo sur Monkeychat._<br />

Ancora un altro utente potrebbe essere molto dettagliato nella conversazione:

_Utente: Salve Siri, possibile, si me un Ottimizza per dimensione e avviare l'app MonkeyChat per inviare un SMS con il messaggio, inviare più bananas._<br />
_Siri: A chi?_<br />
_Utente: Mio migliore alla pubblicazione Bobo._<br />

Inoltre, esistono molti modi che Siri può rispondere a una richiesta, alcuni basato sul modo in cui è stata effettuata la richiesta:

- **Tenendo premuto il pulsante Home** -Siri fornirà più risposte visual con commenti verbali limitato.
- **Per "Ehi Siri"** -Siri verrà più verbale e fornire risposte visual meno.

Siri è ottimizzato anche per soddisfare le esigenze di accessibilità dell'utente e verranno interagire e rispondere base a tali esigenze.

Indipendentemente dal modo in cui viene effettuata una richiesta o come Siri risponde alla richiesta, Siri gestisce la conversazione con l'utente e l'app (tramite le estensioni) fornisce la funzionalità.

Quando l'utente effettua una richiesta verbale di Siri, questi sono i passaggi successivi Siri:

[![](understanding-sirikit-images/monkeychat02.png "I passaggi successivi Siri")](understanding-sirikit-images/monkeychat02.png#lightbox)

1. In primo luogo, Siri accetta l'audio dell'utente **vocale** e lo converte in testo.
2. Successivamente, il testo viene convertito in un **finalità**, un oggetto strutturato rappresentazione della richiesta dell'utente.
3. In base all'intento, Siri richiederà **azione** per eseguire la richiesta dell'utente.
4. Infine, verrà presentato Siri **risposte** (visual e verbali) all'utente in base all'azione eseguita.

Esistono tre modi principali in cui le app possono essere incluse nella conversazione dell'utente con Siri:

[![](understanding-sirikit-images/monkeychat03.png "I tre modi principali che l'app può partecipare alla conversazione gli utenti con Siri")](understanding-sirikit-images/monkeychat03.png#lightbox)

1. **Vocabolario** -si tratta di come l'app indica a Siri di parole è necessario conoscere per interagire con essa.
2. **App per la logica** : queste sono le azioni e le risposte che l'app verrà eseguita in base a determinati Intent.
3. **Interfaccia utente** -si tratta dell'interfaccia utente personalizzata facoltativa che l'app consente di fornire le relative risposte.

### <a name="example"></a>Esempio

In base alle informazioni precedenti, esaminare l'interazione della conversazione seguente con l'app MonkeyChat:

_Utente: Salve Siri, invia un messaggio a Bobo su MonkeyChat._<br />
_Siri: Ciò che si desidera comunicare al Bobo?_<br />
_Utente: Inviare ulteriori bananas._<br />

Il ruolo prima che l'app si assume nella conversazione consiste nel comprendere Siri vocale dell'utente:

[![](understanding-sirikit-images/monkeychat04.png "Aiutare a Siri di comprendere il riconoscimento vocale degli utenti")](understanding-sirikit-images/monkeychat04.png#lightbox)

Siri non ha il nome "Bobo" in un database, ma l'app quindi ha condiviso queste informazioni con Siri tramite il vocabolario. L'app consente anche Siri riconoscere che Bobo è un destinatario, perché ha specificato tali a Siri come un *contatto*.

Siri sa che è necessario eseguire altre per inviare un messaggio a un destinatario, in modo rapido verificherà con l'estensione dell'App per vedere se un messaggio richiede contenuto. Perché non ne MonkeyChat, Siri risponderà all'utente con la domanda: *"Cosa vuoi si dicono Bobo?"*

Nell'esempio precedente, l'utente ha risposto, *", inviare più bananas"*, cui Siri verrà riunire in un strutturato **finalità**:

[![](understanding-sirikit-images/monkeychat05.png "Siri verrà aggregare la risposta dell'utente in un programma strutturato")](understanding-sirikit-images/monkeychat05.png#lightbox)

L'intento strutturato conterrà le informazioni seguenti:

- **Dominio:** messaggi
- **Scopo:** sendMessage
- **Destinatario:** Bobo
- **Contenuto:** , inviare più bananas

Ogni dominio ha come set di sapere *azioni* che può essere eseguita all'interno di essi e basati su dominio e l'azione, zero a molti parametri potrebbero essere inclusi nell'intenzione inviata all'app.

Lo scopo viene quindi inviato per l'estensione dell'App per l'elaborazione. Come risultato dell'elaborazione lo scopo, l'app genera una **IntentResponse** che sarà possibile raggruppare queste con lo scopo e includono parametri che descrivono cosa hanno fatto l'app con lo scopo.

Ogni IntentResponse includerà anche un **codice di risposta** indicherà se l'app è stata in grado di completare la richiesta o non Siri. Alcuni domini sono codici di risposta di errore molto specifici che possono essere inviati anche.

Infine, il IntentResponse includerà un `NSUserActivity` (simile a quelle utilizzate per supportare mano Off). Il `NSUserActivity` verrà usato per avviare l'app, se la risposta è necessario lasciare l'ambiente di Siri e immettere l'app per completare l'operazione. 

Siri compilerà automaticamente appropriata `NSUserActivity` per avviare l'app e il prelievo punto in cui l'utente interrotta nell'ambiente di Siri. Tuttavia, l'app può fornire il proprio `NSUserActivity` con personalizzate, se necessario.

Dopo che l'app ha elaborato la finalità e ha restituito una risposta a Siri, quindi presenta i risultati all'utente (verbalmente e visivamente):

[![](understanding-sirikit-images/monkeychat06.png "I risultati presentati all'utente verbalmente sia visivamente")](understanding-sirikit-images/monkeychat06.png#lightbox)

Siri ha risposta predefinita diverse interfacce utente per ognuno dei domini disponibili per l'app. Tuttavia, poiché MonkeyChat ha fornito un'estensione dell'interfaccia utente Intent facoltativa, viene usato per presentare i risultati della conversazione all'utente nell'esempio precedente.

## <a name="the-intent-lifecycle"></a>Il ciclo di vita preventivo

Esistono tre attività principali che l'estensione dell'App da eseguire quando si lavora con gli Intent:

[![](understanding-sirikit-images/monkeychat07.png "Il ciclo di vita preventivo")](understanding-sirikit-images/monkeychat07.png#lightbox)

1. L'app deve **risolvere** ogni parametro su un evento. Di conseguenza, l'app chiamerà Resolve più volte (una volta per ogni parametro) e talvolta più volte per lo stesso parametro fino a quando l'app e l'utente concordano su ciò che viene richiesto.
2. L'app deve **Confirm** che può gestire l'intento richiesto e raccontare Siri il risultato previsto.
3. Infine, l'app deve **gestire** la finalità ed eseguire i passaggi per ottenere il risultato richiesto.

### <a name="the-resolve-stage"></a>La fase di risoluzione

La fase di risoluzione consente a Siri di conoscere i valori che l'utente ha fornito e assicura che l'utente deve effettivamente cosa succede quando lo scopo viene elaborato dall'app. 

Questa fase fornisce anche un'opportunità per l'app influenzare il comportamento di Siri durante la conversazione con l'utente. A tale scopo, l'app offrirà un' **risposta risoluzione**. Esistono una serie di risposta predefinita per i diversi tipi di dati in grado di interpretare Siri.

I più comuni risoluzione risposta dall'app sarà **Success**, vale a dire l'app corrispondenze per i dati specifici da un parametro (ad esempio nome utente della schermata) a un'informazione riconosca.

Potrebbero esserci volte quando l'app deve verificare che una determinata richiesta corrisponda all'informazione corretta che riconosca. In questi casi, invierà un **ConfirmationRequired** risposta per inviare una domanda Sì o no per l'utente, ad esempio *"Inviare il messaggio a Bobo Ovest?"*

Potrebbero esserci altri casi in cui l'app richiederà all'utente di scegliere da un breve elenco di opzioni. In questo caso, l'app offrirà un' **Disambiguazione** risposta con un elenco di opzioni di due a 10 per l'utente di scegliere, ad esempio: 

```csharp
Who do you want to message?

* Bobo the Great
* Bobo Jr.
* Little Bobo
```

Siri gestirà l'utente che effettua la selezione, verbalmente o tramite l'interazione con l'UI Siri, e il risultato verrà inviato nuovamente all'app.

In altri casi, potrebbe non esserci informazioni sufficienti per l'app risolvere il parametro oppure potrebbero esserci corrisponde a un numero eccessivo di risolvere utilizzando disambiguazione (ad esempio, 80 utenti con Bobo nel nome). In questo caso, l'app invierà un **NeedsMoreDetails** Siri e risposta richiederà all'utente di essere più specifici.

Se l'utente non specifica un valore che è necessario per tale processo lo scopo, è possibile inviare un **NeedsValue** risposta affinché Siri richiedere all'utente per il valore.

Se l'app non supporta un valore che l'utente ha dato per un parametro specifico, è possibile inviare il **UnsupportedWithReason** risposta per specificare un motivo per cui il valore non era supportato. Siri richiederà quindi all'utente per un valore completamente nuovo e date loro il motivo è obbligatorio.

Usare infine il **NotRequired** risposta per indicare che l'app non richiede un valore per un determinato parametro a Siri. Se l'utente fornisce comunque uno, verrà semplicemente ignorato da Siri.

### <a name="the-confirm-stage"></a>La fase di conferma

La fase di conferma ha due scopi:

- Per indicare il risultato previsto di gestire un Intent in modo che Siri può informare l'utente che si desidera venga eseguito a Siri.
- Fornisce un controllo opportunità alcuno stato richiesto l'app potrebbe essere necessario per completare la richiesta presentata dall'utente, ad esempio la presenza di fondi sufficienti nella banca per effettuare il pagamento richiesto.

L'app offrirà un' **con finalità di risposta** nel passaggio di conferma, che deve essere popolato con tutte le informazioni dell'app abbia disponibile affinché Siri possa comunicare, in modo efficace con l'utente.

Basato sul tipo di dominio e l'azione, Siri possono richiedere all'utente per la conferma, ad esempio prima di inviare un pagamento o di una questione di prenotazione.

### <a name="the-handle-stage"></a>La fase di Handle

La fase di gestione è la parte più importante dell'utilizzo di un Intent perché è il punto in cui l'app soddisfa la richiesta dell'utente eseguendo l'attività che è stato richiesto di eseguire.

Esattamente come accade in fase di conferma, l'app deve fornire quante più informazioni sul risultato possibile in modo Siri ciò può essere correlata all'utente. A volte queste informazioni verranno presentate visivamente o in altri casi Siri verrà semplicemente pronunciare all'utente. 

Potrebbero esserci volte quando l'app può richiedere più tempo per elaborare una determinata richiesta, ad esempio ritardi di chiamata di rete o se necessita di una persona in tempo reale soddisfare la richiesta (ad esempio, il completamento e un ordine di spedizione o guidare un'auto per la posizione dell'utente). Quando Siri attende una risposta di app, verranno visualizzati un'interfaccia utente in attesa per l'utente che comunica che l'app sta elaborando la richiesta.

In teoria, l'app deve fornire una risposta a Siri all'interno di due o tre secondi al massimo. Se l'app conosce che sta per richiedere più tempo per elaborare una determinata risposta, è necessario inviare un' **InProgress** codice di risposta a Siri. Siri quindi l'utente viene informato che l'app sta elaborando la richiesta in background e continuerà a eseguire questa operazione anche se gli utenti lasciano l'ambiente di Siri.

## <a name="adding-sirikit-to-the-app"></a>Aggiunta di SiriKit all'App

Con SiriKit in iOS 10, Apple ha creato due nuovi punti di estensione:

- **Estensione per Intent** - fornisce Siri con il contenuto dell'app ed esegue le attività necessarie per soddisfare qualsiasi Intent supportati.
- **Estensione per interfaccia utente Intent** -fornisce un'interfaccia utente personalizzata che verrà visualizzata per il contenuto delle App all'interno di Siri. 

È anche disponibile un'API per fornire parole e frasi a Siri di aiuto nel riconoscimento sotto forma di:

- **App vocabolario** -parole o frasi che sono comuni a tutti gli utenti dell'app.
- **Utente vocabolario** -parole e frasi che sono univoche per un utente dell'app specificato.

## <a name="the-intents-extension"></a>L'estensione per Intent

L'estensione per Intent è responsabile della gestione delle interazioni principali tra l'app e Siri nel modo seguente:

[![](understanding-sirikit-images/intents01.png "L'estensione per Intent")](understanding-sirikit-images/intents01.png#lightbox)

L'estensione per Intent può supportare uno o più finalità, spetta allo sviluppatore di decidere come desiderano implementare SiriKit nell'app. Lo sviluppatore è stato possibile aggiungere anche un'estensione per Intent separato per ogni scopo che devono essere gestiti.  Ciò premesso, Apple richiede che lo sviluppatore di limitare il numero di estensioni con finalità di in modo che non sia Siri già aperto per l'app, che richiedono più memoria e tempo per gestire più processi.

Lo sviluppatore deve anche tenere presente che l'estensione per Intent verranno eseguiti in background mentre Siri è attiva. Questo consente a Siri di eseguire in modo attivo per una conversazione con l'utente durante la comunicazione comunque con l'estensione per elaborare le informazioni relative alla richiesta.

## <a name="privacy-and-security-considerations"></a>Considerazioni sulla sicurezza e privacy

Apple ha intrapreso eccezionale per garantire che un utente privato informazioni siano protette quando si lavora con Siri e di conseguenza, esistono interazioni più che l'utente dovrà essere effettuato nel dispositivo iOS. Ad esempio, quando si richiede una giostra o effettuare un pagamento.

Inoltre, sono presenti comportamenti specifici che l'app potrebbe essere necessario limitare l'utente viene connesso al dispositivo. Per queste situazioni, l'app può richiedere il **limitare durante il blocco** comportamento. Questa operazione viene eseguita tramite un'impostazione nel `Info.plist` file.

Il Framework di autenticazione locale è disponibile per l'estensione per Intent in modo che l'app può chiedere all'utente le informazioni di autenticazione aggiuntivo, anche se il dispositivo è già sbloccato.

Infine, Apple Pay è disponibile per l'estensione per Intent in modo che l'app può completare una transazione usando Apple Pay e il foglio di Apple Pay predefinito verrà visualizzati sopra l'interfaccia Siri.

Inoltre, Apple vuole assicurarsi che gli utenti sia quando si inviano informazioni per un'app di terze parti 3rd e di conseguenza, l'utente corrente **deve** pronunciare il nome specifico dell'app (come specificato nel nome visualizzato del Bundle dell'app) quando effettua una richiesta.

Apple ha progettato Siri per eseguire conversazioni naturale e fluide con l'utente e per questo motivo, nome del Bundle dell'app può essere utilizzato in molte parti del discorso, ogni volta che quest'ultima rientri modo naturale nella richiesta dell'utente.

Uno degli aspetti comuni che gli utenti faranno consiste nella "verbify" il nome dell'applicazione, in altre parole, accettando il nome dell'app e l'uso come un verbo in una richiesta. Ad esempio, *"MonkeyChat Bobo è stato un ottimo bananas."*

## <a name="the-intents-ui-extension"></a>L'estensione per interfaccia utente Intent

L'estensione per interfaccia utente Intent presenta la possibilità di portare l'interfaccia utente dell'app e informazioni personalizzate distintive nell'esperienza Siri e rendere gli utenti è connessa all'app. Con questa estensione, l'app può portare il marchio, nonché informazioni visual e altri nella trascrizione.

[![](understanding-sirikit-images/intents02.png "Output di esempio estensione dell'interfaccia utente Intent")](understanding-sirikit-images/intents02.png#lightbox)

L'estensione per interfaccia utente Intent restituirà sempre un `UIViewController` e l'app può aggiungere qualsiasi operazione all'interno di controller di visualizzazione, ad esempio che mostra informazioni aggiuntive che vanno oltre la risposta iniziale. L'interfaccia utente Intent possono anche aggiornare l'utente con lo stato di un evento di esecuzione prolungata, ad esempio quanto tempo richiederà una giostra automobile per raggiungere il percorso di condivisione.

L'estensione per interfaccia utente Intent verrà sempre visualizzato insieme ad altro contenuto Siri, ad esempio l'icona dell'app e il nome nella parte superiore dell'interfaccia utente o, in base all'intento, pulsanti, come trasmissione o Annulla, potrebbero essere visualizzati nella parte inferiore.

Sono presenti poche istanze in cui l'app può sostituire le informazioni che viene visualizzato all'utente per impostazione predefinita, ad esempio messaggistica di Siri o viene eseguito il mapping in cui l'app può sostituire l'utilizzo predefinito con uno su misura per l'app.

> [!IMPORTANT]
> Sebbene sia possibile aggiungere elementi interattivi, ad esempio `UIButtons` oppure `UITextFields` per l'estensione dell'interfaccia utente Intent `UIViewController`, questi vengono rigorosamente non consentiti come l'interfaccia utente Intent in modalità interattiva e l'utente non sarà in grado di interagire con essi.

È completamente facoltativo per l'app fornire un'estensione dell'interfaccia utente Intent poiché Siri contiene un set predefinito di interfaccia utente per ogni tipo di Intent. Inoltre, le interfacce dell'interfaccia utente Intent sono disponibili solo per determinati Intent che Apple ha ritenuto possono risultare utili per l'utente.

## <a name="adding-sirikit-vocabulary"></a>Aggiunta del vocabolario di SiriKit

L'ultima parte dell'implementazione di SiriKit si trova all'interno dell'app, fornendo il vocabolario necessario. Molte App hanno univoci modi in cui descrivere le informazioni dell'utente e i modi univoci che l'utente fornirà le informazioni per l'app.

Per questo motivo, Siri richiede l'assistenza dell'app per comprendere le parole e frasi univoche per l'app. Alcune di queste frasi farà parte dell'app in modo che ogni utente sa e comprenderne il funzionamento. Ancora ad altri utenti siano univoci per un determinato utente dell'app.

### <a name="app-specific-vocabulary"></a>Vocabolario specifico dell'App

La specifica terminologia App definisce le parole o frasi specifiche che saranno noto a tutti gli utenti dell'app, ad esempio per gli allenamenti nomi o tipi di veicoli. Poiché questi fanno parte dell'applicazione, vengono definiti un `AppIntentVocabulary.plist` file come parte del bundle dell'app principale. Inoltre, queste parole e frasi devono essere localizzate.

Esistono diverse parti di un vocabolario `AppIntentVocabulary.plist` file:

- **App di esempio Usa** -fornisce un insieme di casi d'uso comuni per le richieste che l'utente possa prendere dell'app. Ad esempio: *"Avviare accinge con MonkeyFit".*
- **Parametri** -offrono un set di tipi di parametro non standard specifici per l'app. Ad esempio, per gli allenamenti i nomi per l'app MonkeyFit. Questi sono:
    - **Frase** -consente all'app di definire termini univoci per l'app. Ad esempio: il tipo per gli allenamenti "Bananarific" per l'app MonkeyFit. 
    - **Pronuncia** -consente l'hint per la pronuncia a Siri come una semplice ortografia fonetica di una frase specifica. Ad esempio, "ba nana delle istanze riservate fico".
    - **Esempio** -fornisce un esempio dell'uso di frase specifica nell'app. Ad esempio, *"Avviare un Bananarific in MonkeyFit"*.

Per altre informazioni, vedere di Apple [riferimento sul formato di File vocabolario App](https://developer.apple.com/library/prerelease/content/documentation/Intents/Conceptual/SiriIntegrationGuide/CustomVocabularyKeys.html#//apple_ref/doc/uid/TP40016875-CH10-SW1).

### <a name="user-specific-vocabulary"></a>Utente specifica terminologia

La specifica terminologia utente dovrà fornire parole o frasi specifiche dei singoli utenti dell'app. Questi vengono forniti in fase di esecuzione dell'App principale (non le estensioni App) come un set ordinato di termini ordinate una priorità di utilizzo più significativi per gli utenti, con i termini più importanti all'inizio dell'elenco.

Esaminiamo l'esempio di app MonkeyChat presentato in precedenza. MonkeyChat mantiene un elenco di tutti i contatti dell'utente, che invierà a Siri tramite la specifica terminologia utente. Inoltre mantiene un elenco dei contatti più recente 10 che l'utente dispone di messaggi e presenta una serie di contatti ai Preferiti per ciascun utente. Per questo esempio, i contatti Preferiti devono essere all'inizio del nostro vocabolario specifico utente, aggiungendo i contatti recenti quindi il resto dei contatti dell'utente.

Sono supportati i seguenti tipi di informazioni da utente specifica terminologia:

- Nomi dei contatti.
- Per gli allenamenti nomi.
- Nomi degli Album di foto.
- Parole chiave di foto.

Se l'app si basa su iOS Address Book, l'app non sarà necessario intraprendere alcuna azione, poiché queste informazioni sono già disponibili a Siri. L'app deve solo fornire nomi di contatti se l'app ha un proprio database univoco dei contatti.

Quando si progetta il vocabolario, è necessario fornire solo i valori necessari che gli utenti di conosceranno e interessano. Evitare di fornire informazioni quali i numeri di telefono o indirizzi di posta elettronica.

L'app dovrà anche aggiornare prontamente Siri quando viene modificato vocabolario specifico utente. Gli utenti sono abituati a eseguire la richiesta di informazioni da Siri nel momento in cui è stato aggiunto al dispositivo iOS. Ad esempio, se l'utente aggiunge un nuovo contatto nell'app, inviare tali informazioni a Siri, non appena l'utente lo salva.

Ancora più importante, l'app _necessario_ eliminare le informazioni dal vocabolario Siri tempestivamente il cliente poiché un utente può diventare neppure deludere se vengono eliminati un'informazione ma Siri è stata ancora riconoscendo, ore o giorni in un secondo momento.

> [!IMPORTANT]
> L'app deve rimuovere tutti il vocabolario specifico utente da Siri se l'utente sceglie di reimpostare l'app o se è la disconnessione.

## <a name="sirikit-permissions"></a>Autorizzazioni di SiriKit

L'ultima parte di SiriKit è incentrata sulla autorizzazioni. Come usare altre funzionalità di iOS (ad esempio foto, fotocamera o contatti), gli utenti devono concedere l'autorizzazione esplicita per l'app comunicare con Siri.

L'app è in grado di fornire una stringa che definisce quali informazioni forniranno a Siri e fornire un motivo per il motivo per cui l'utente deve concedere l'accesso. 

L'app deve richiedere l'autorizzazione da parte dell'utente da utilizzare Siri la prima volta che l'utente apre l'app sono stati aggiornati a iOS 10 suggerite da Apple. Si tratta in modo che gli utenti conoscono l'integrazione di Siri e possono utilizzo approvato prima di apportare la prima richiesta.

## <a name="sirikit-and-maps"></a>SiriKit e mappe

SiriKit è parte integrante di iOS e Usa il framework Intent maggiori aggiunto ai dispositivi iOS 10. Il framework Intent è stato progettato per condividere gli Intent e azioni comuni e condivise con altre parti del sistema.

Il framework Intent va oltre appena integrazione Siri e fornisce altre funzionalità come l'integrazione di contatti in cui l'app può diventare la telefonia predefinito o le app di messaggistica per i contatti specifici. Gli Intent forniscono inoltre una profonda integrazione con CallKit per fornire agli utenti la migliore esperienza VOIP possibile.

L'app esegue il mapping in iOS 10 ha aggiunto funzionalità, ad esempio rani condivisione in cui l'utente può prenotare una giostra direttamente all'interno dell'interfaccia utente di mappe. SiriKit fornisce un punto di estensione comuni con le mappe in modo che gli intenti rani condivisione (e altri) possono essere condivisi tra Siri e mappe. 

Ciò significa che se l'app ha adottato le estensioni di SiriKit, riceveranno anche l'integrazione con mappe gratuitamente. 

## <a name="designing-a-great-siri-experience"></a>Progettazione di una straordinaria esperienza di Siri

Progettazione di un'esperienza utente eccezionale durante l'integrazione di un'app in Siri è diversa dalla progettazione di un'interfaccia utente di app eccezionali. A differenza dei normali situazioni in cui l'utente interagisce con l'app direttamente dello schermo, quando si usa Siri vi sono molte volte quando nessuna interfaccia visiva è visibile in tutti. Ad esempio, quando l'utente ha avviato la conversazione restituendo *"Ehi Siri"*.

### <a name="how-siri-helps-the-developer"></a>Come Siri consente allo sviluppatore

Quando si progettano le interazioni di un'app con Siri, l'app verrà compilata un' *interfaccia discorsiva*, vale a dire il contesto è derivato dalla conversazione a cui si verificano Siri con l'utente per conto dell'app.

In assenza di un riferimento visivo, l'utente deve tenere traccia di informazioni in corso presentate nella propria testa. Per questo motivo, Siri presenta le informazioni minime necessarie per ottenere l'attività utente è che desiderano eseguire.

L'interfaccia discorsiva la forma per le domande e risposte da Siri sia l'utente durante la conversazione. Pertanto, è importante considerare come Siri vengono poste delle domande e risponde durante la progettazione di questa interfaccia.

L'esempio seguente dell'utente che crea un messaggio, Siri potrebbe rispondere alla domanda: *"Pronto per inviarlo?"* . L'utente può rispondere in molti modi diversi, ad esempio *"Invia"*, *"Annulla"* o persino completamente non correlati a questa domanda. Indipendentemente dal modo in cui la conversazione viene riprodotto, Siri gestirla per l'app e inviarla solo le informazioni rilevanti appena sarà disponibile.

Esistono diversi modi che un utente può avviare una conversazione con Siri:

- Con i backup del dispositivo, premendo il pulsante Home. In questa situazione Siri presenterà interfacce visual più e meno risposte verbali.
- Affermando *"Ehi Siri"* e l'avvio di una conversazione mani gratuito. In questa situazione Siri sarà meno visual e più verbali.
- Utilizzo delle funzionalità di accessibilità, ad esempio bluetooth abilitato aids udito in cui l'interfaccia utente sarà essere adattata per un utente con particolari esigenze.
- Usando Auto Play in cui l'utente deve mantenere l'attenzione concentravamo sul migliorare, mantenendo al minimo gli elementi di distrazione.

### <a name="how-the-developer-helps-siri"></a>Come sviluppatore consente a Siri

Quando si integra un'app con Siri, lo sviluppatore deve testare questa integrazione spesso e assicurarsi che si tratta di molte richieste diverse richiedendo la stessa porzione di informazioni o attività in diversi modi possibili.

Poiché nessuna due persone di interazione utente nello stesso modo, è fondamentale che lo sviluppatore ottiene così tante diversi beta tester possibili per consentire di ottimizzare l'integrazione di Siri. Gli utenti possono richiedere informazioni o inoltrare richieste in modo lo sviluppatore tuttavia mai di e questa ottimizzazione può garantire che il gruppo più ampio di utenti abbiano un'esperienza ottimale con le app con Siri.

In diverse situazioni e ambienti di test. Avviare le conversazioni con Siri in tutti i modi possibili per assicurarsi che queste conversazioni rimangano fluido e naturali. Testare in posizioni in cui l'utente molto probabilmente utilizzeranno l'app, come in un palestra affollato.

Assicurarsi che l'app fornisce tutte le informazioni necessarie per rappresentare correttamente la richiesta e il risultato all'utente di Siri. Ciò vale soprattutto quando si usa Siri in una situazione mani gratuito.

### <a name="siri-design-guidelines"></a>Linee guida di progettazione di Siri

Ricordare sempre che Siri è avere una conversazione con l'utente per conto dell'app. Lo sviluppatore desidera certi che la conversazione rimane come fluide e naturale possibile.

Come si farebbe con qualsiasi conversazione importante, lo sviluppatore deve verificare quanto segue:

- Che l'app è pronto per la conversazione.
- Che l'app è in ascolto esattamente ciò che l'utente sta provando a eseguire.
- Che l'app chiede le domande appropriate nel momento appropriato.
- Se l'app risponde alla richiesta con le informazioni che l'utente cerca.

#### <a name="preparing-for-the-conversation"></a>Preparazione per la conversazione

La prima cosa da ricordare è che gli utenti dell'app non si intende usare esattamente come gli sviluppatori. Potrebbero provenire da diversi sfondi, utilizzano lingue diverse o avere esigenze particolari quando si lavora con l'app.

Inoltre, poiché lo sviluppatore progettata e compilata l'app, hanno profonda conoscenza approfondita dell'app e il funzionamento interno e funzioni che un utente standard non avrà. Pertanto, lo sviluppatore potrebbe chiedere in modo diverso rispetto a un utente normale richiesta di Siri.

È per questo motivo è fondamentale avere perché molte persone diverse possibile interagiscono con l'app tramite Siri. Gli utenti possono eseguire le richieste dell'app tramite Siri che lo sviluppatore non ha mai pensato di o in modi che non prendere in considerazione lo sviluppatore.

#### <a name="ensure-the-app-is-a-good-listener"></a>Verificare che l'App è un buon Listener

Lo sviluppatore deve assicurarsi che l'app è un buona listener e sta ricevendo le specifiche della conversazione che soddisfano le aspettative dell'utente. Ma è anche possibile potrebbe non avere fornito tutte le informazioni che l'app richiede per ottenere l'attività richiesta.

Esistono diversi modi, che l'applicazione potesse gestire questa situazione:

- **Selezionare un'impostazione predefinita corretta per il valore mancante** : ad esempio una giostra app di condivisione potrebbe predefinito alla posizione corrente dell'utente se non specificano in cui vuole essere recuperate da.
- **Assicurarsi di potere indovinare** -usando informazioni specifiche che l'app ha raccolto per l'utente, l'app potrebbe essere in grado di marca e il potere indovinare le informazioni mancanti, ad esempio la compilazione in un numero di cellulare mancano da informazioni di contatto dell'utente. Tuttavia, devono prestare attenzione per evitare sorprese non valide, ad esempio selezionando l'opzione più costosa e così via.
- **Messaggio di richiesta per più informazioni** -l'app può avere Siri richiedere all'utente per il valore mancante. Tuttavia, la chiave qui mantiene le conversazioni semplici e per il punto. Gli utenti rapidamente diventerà frustrati qualora fosse necessario rispondere a diverse domande per ottenere la richiesta.
- **Gestire correttamente delle** -l'utente potrebbe fornire un valore che non era previsto l'app o che non può gestire nel contesto specificato. Assicurarsi che l'app si riferisce all'utente in modo che rende semplice e chiaro per lui per risolvere questa situazione.

Quando l'app viene presentato con un singolo valore in questione, il modo migliore per gestire questa situazione si deve Siri chiedere conferma all'utente. Ad esempio, *"Si intendeva Bobo Ovest?"* , che è possibile rispondere con una semplice risposta positiva o negativa.

Quando si verifica una situazione in cui le scelte possibili diversi può essere corrette per un singolo valore, disambiguazione è il metodo di gestione preferiti. In questa situazione Siri può chiedere all'utente con fino a dieci possibili opzioni tra cui scegliere. Ad esempio:

```csharp
Who do you want to send the message to?

* Bobo the Great!
* Bobo Jr.
* Little Bobo
```

Se ancora in questione, avere Siri richiedere all'utente di fornire una risposta completamente nuova, più specifica per un determinato valore.

#### <a name="request-final-confirmation"></a>Richiedere conferma finale

Prima che l'app esegue l'attività per soddisfare la richiesta dell'utente, si controllerà Siri con l'estensione dell'App per assicurarsi che tutto sia attivo. Ad esempio, l'utente ha fondi sufficienti nel proprio account per la richiesta con il pagamento?

Inoltre, l'app deve garantire che fornisce tutte le informazioni possibili a Siri presentarli all'utente e verificare che l'attività sta per essere eseguito soddisfi le aspettative in modo.

Una volta che l'utente ha confermato la richiesta e l'app ha è stata eseguita l'app dovrà anche in questo caso assicurarsi che lo ha fornito tutti i risultati a Siri in modo che possa associarli all'utente.

#### <a name="responding-to-the-request"></a>Risposta alla richiesta

Siri dispone di diverse interfacce utente predefinite per ognuno dei domini e le azioni che a conoscenza. Tuttavia, laddove appropriato, l'app può fornire un'estensione dell'interfaccia utente Intent personalizzata per arricchire l'esperienza utente presentando branding dell'app e dell'interfaccia utente o altre informazioni rispetto a quanto fosse presente nella richiesta.

Ciò premesso, moderazione deve essere utilizzato durante la progettazione di interfacce personalizzate per Siri. In genere, l'utente vuole ottenere una specifica attività eseguite nel minor tempo e non si vuole eseguire l'overload con le informazioni non necessarie.

Deve anche essere attenzione per assicurarsi che l'interfaccia utente personalizzata è simile e risponda correttamente in tutti i dispositivi iOS di proprietà diversi e gli orientamenti che l'utente potrebbe avere o usare il dispositivo.

Quando appropriato, usare l'API di SiriKit per nascondere le informazioni ridondanti già presenti nella UI Siri predefinita. Ancora verificare che l'app è ancora offrano le informazioni relative a Siri in modo possibile presentarla verbalmente in situazioni un pratico gratuito.

Potrebbero esserci situazioni in cui Siri avvierà l'app per soddisfare la richiesta dell'utente, ad esempio la presentazione di foto che l'utente ha richiesto. In queste situazioni, non sorprendere all'utente. Visualizzare le informazioni previste senza ulteriore intervento o passaggi intermedi. Non visualizza mai informazioni o eseguire un'attività per che l'utente non è previsto.

### <a name="polishing-the-design"></a>La progettazione di gestione

Esistono diverse operazioni che suggeriscono di Apple per la progettazione delle interfacce discorsive polacco. È in primo luogo, fornendo chiaro e concisi del vocabolario e usare esempi di casi a Siri.

Uno dei modi che un utente individua l'app è avviando una conversazione con Siri e in cui viene chiesto, *"Cosa si può fare?"* Siri mostrerà varie situazioni è possibile eseguire, ad esempio un'app per gli sviluppatori e hero di esempio utilizza i case che lo ha fornito tramite relativo `plist` file.

Come scrivere i casi d'uso di esempio valido:

- Assicurarsi che gli esempi includono il nome dell'app.
- Mantenere l'esempio breve e a punto.
- Fornire più esempi per ognuna delle finalità supportata dall'app.
- La priorità sia l'Intent e gli esempi in base a casi di uso più comuni per l'app.
- Assicurarsi che l'app vengono forniti esempi localizzati.
- Assicurarsi che ogni esempio riportato funziona come previsto all'interno dell'app.
- Evitare di indirizzamento Siri negli esempi, in modo che non includono testo, ad esempio *"Ehi Siri..."*
- Evitare, ad esempio qualsiasi pleasantries inutili *","* oppure *"Grazie per aver"*.

Dedicare del tempo appropriato per esplorare e sperimentare nel modo in cui l'app può modellare la conversazione in presenza di Siri con l'utente per suo conto. Assicurarsi di comunicare con gli utenti tipici nell'intero processo, come le relative interazioni con e le aspettative dell'app potrebbero cambiare nel corso del tempo.

È importante testare l'app in situazioni diverse e tutti i diversi metodi per richiamare una conversazione con Siri. Test nelle posizioni reali dell'utente usino l'app, lontano da ufficio e supporto tecnico.

Cercare una possibilità fluido, naturale e "difficoltà nell'utilizzare semplicemente" le conversazioni con Siri (per conto dell'app). 

## <a name="summary"></a>Riepilogo

Questo articolo ha illustrato i concetti principali necessari per usare SiriKit e mostrato che può interagire con le app xamarin. IOS per fornire servizi accessibili all'utente tramite Siri e all'app mappe su un dispositivo iOS.




## <a name="related-links"></a>Collegamenti correlati

- [Esempio ElizaChat](https://developer.xamarin.com/samples/monotouch/ios10/ElizaChat/)
- [Guida per programmatori di SiriKit](https://developer.apple.com/library/prerelease/content/documentation/Intents/Conceptual/SiriIntegrationGuide/index.html)
- [Riferimento a Framework Intent](https://developer.apple.com/reference/intents)
- [Riferimento a Framework dell'interfaccia utente Intent](https://developer.apple.com/reference/intentsui)
