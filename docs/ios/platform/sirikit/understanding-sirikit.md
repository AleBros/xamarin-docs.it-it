---
title: Informazioni sui concetti SiriKit
description: "In questo articolo descrive i concetti chiavi che verrà richiesto per l'utilizzo di SiriKit in un'app xamarin. IOS."
ms.topic: article
ms.prod: xamarin
ms.assetid: 99EC5C1E-484F-4371-8555-58C9F60DE37F
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/16/2017
ms.openlocfilehash: 36d8e27ce06e38e1cf652558bfb5a83f572e4403
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2018
---
# <a name="understanding-sirikit-concepts"></a>Informazioni sui concetti SiriKit

_In questo articolo descrive i concetti chiavi che verrà richiesto per l'utilizzo di SiriKit in un'app xamarin. IOS._


Nuovo in iOS 10, SiriKit consente un'app xamarin di fornire servizi sono accessibili all'utente mediante Siri e l'app esegue il mapping in un dispositivo iOS. Questa funzionalità viene fornita in uno o più estensioni di App utilizzando il nuovo **intenti** e **dell'interfaccia utente intenti** Framework.

SiriKit consente a un'app iOS fornire servizi che sono accessibili all'utente mediante Siri e l'app esegue il mapping in un dispositivo iOS mediante le estensioni App e il nuovo **intenti** e **dell'interfaccia utente intenti** Framework.

Siri funziona con il concetto di **domini**, sapere di gruppi di azioni per attività correlate. Ogni interazione con l'app con Siri deve assumere uno dei domini servizio noto come indicato di seguito:

- Audio o video chiamata.
- Prenotazione di un interscambio.
- Gestione allenamenti.
- Messaggistica.
- Ricerca foto.
- Inviare o ricevere i pagamenti.

Quando l'utente effettua una richiesta di Siri che include uno dei servizi dell'estensione App, SiriKit invia l'estensione un **finalità** oggetto che descrive la richiesta dell'utente insieme a eventuali dati di supporto. L'estensione dell'App, quindi genera appropriata **risposta** dell'oggetto per il determinato **finalità**, che riporta in dettaglio come l'estensione può gestire la richiesta.

## <a name="the-intents-and-intents-ui-extensions"></a>I tipi ed estensioni di tipi dell'interfaccia utente

Siri sia l'app mappe interagire con i servizi dell'applicazione tramite due diversi tipi di estensioni dell'App:

- **Estensione intenti** -Siri fornisce e mappe con l'app del contenuto e vengono eseguite le attività necessarie per soddisfare qualsiasi dei tipi supportati.
- **Estensione dell'interfaccia utente intenti** -fornisce un'interfaccia utente personalizzata che verrà visualizzata per il contenuto dell'app all'interno di Siri o mappe.

L'applicazione deve fornire un'estensione di tipi per supportare SiriKit ed è responsabile per fornire informazioni che Siri e mappe possono presentare all'utente e per la gestione dei tipi.

Creazione di un'estensione dell'interfaccia utente intenti è facoltativa poiché Siri in genere gestisce tutte le interazioni utente e ha un'interfaccia utente predefinita standard per la presentazione di informazioni in ognuno dei domini supportati. Fornendo un'estensione di tipi di interfaccia utente, è possibile usare l'app di **dell'interfaccia utente con finalità di** framework per presentare un completo e personalizzata interfaccia utente dotato di branding l'app e informazioni aggiuntive.

## <a name="siri-and-the-maps-app-role"></a>Siri e il ruolo App di mappe

Richieste parlata dell'utente sono elaborati e semanticamente analizzata da Siri che consente di trasformare le richieste in tipi utilizzabili in grado di gestire le estensioni con finalità di una lingua.

Maps Usa estensioni finalità dell'applicazione per visualizzare le informazioni nell'interfaccia di mappa in risposta alle azioni dell'utente. Ad esempio richiedere nelle vicinanze ristoranti o per ottenere revisioni del ristorante dell'app.

Siri sia mappe gestire tutte le interazioni dell'utente e visualizzare i risultati tramite l'interfaccia di sistema standard. Il ruolo app di estensioni è principalmente per fornire i dati che viene visualizzati. Facoltativamente, l'app può fornire un'estensione di tipi di interfaccia utente e presentare un'interfaccia utente personalizzata per migliorare l'interfaccia di sistema predefinito.

## <a name="interacting-with-siri-via-sirikit"></a>L'interazione con Siri tramite SiriKit

Questa sezione viene fornita una panoramica delle modalità SiriKit consente all'utente di interagire con l'app usando Siri. Ai fini di questo esempio, verrà usato l'app MonkeyChat fittizio:

[ ![](understanding-sirikit-images/monkeychat01.png "L'icona MonkeyChat")](understanding-sirikit-images/monkeychat01.png)

MonkeyChat mantiene il proprio contatto della Rubrica di amici dell'utente, ognuno associato a un nome di schermata (ad esempio Bobo ad esempio) e consente all'utente di inviare ogni amico chat di testo in base al nome della schermata.

Esistono molti modi che l'utente può avviare un'interazione con l'app, poiché utenti diversi potrebbero essere la stessa richiesta in formati diversi.

Ad esempio, se l'utente vuole inviare un messaggio a loro friend Bobo, potrebbe essere hanno la conversazione con Siri seguente:

<table width="100%" border="1px">
<tr>
    <td width="50%"><b>Siri</b></td>
    <td width="50%"><b>Utente</b></td>
</tr>
<tr>
    <td></td>
    <td>"Hey Siri, invia un messaggio MonkeyChat"</td>
</tr>
<tr>
    <td>"A cui?"</td>
    <td></td>
</tr>
<tr>
    <td></td>
    <td>"Bobo"</td>
</tr>
<tr>
    <td>"Ciò che si desidera si dicono Bobo?"</td>
    <td></td>
</tr>
<tr>
    <td></td>
    <td>"Inviare ulteriori banane"</td>
</tr>
</table>

Un'altra persona potrebbe essere la stessa richiesta con una conversazione diversi:

<table width="100%" border="1px">
<tr>
    <td width="50%"><b>Siri</b></td>
    <td width="50%"><b>Utente</b></td>
</tr>
<tr>
    <td></td>
    <td>"Invia un messaggio a Bobo in MonkeyChat"</td>
</tr>
<tr>
    <td>"Ciò che si desidera si dicono Bobo?"</td>
    <td></td>
</tr>
<tr>
    <td></td>
    <td>"Inviare ulteriori banane"</td>
</tr>
</table>

E un altro utente può effettuare una richiesta ancora più breve:

<table width="100%" border="1px">
<tr>
    <td width="50%"><b>Siri</b></td>
    <td width="50%"><b>Utente</b></td>
</tr>
<tr>
    <td></td>
    <td>"MonkeyChat Bobo inviare ulteriori banane"</td>
</tr>
<tr>
    <td>"Ok, l'invio di messaggi invia ulteriori banane a Bobo in Monkeychat"</td>
    <td></td>
</tr>
</table>

O addirittura la stessa richiesta in una lingua diversa:

<table width="100%" border="1px">
<tr>
    <td width="50%"><b>Siri</b></td>
    <td width="50%"><b>Utente</b></td>
</tr>
<tr>
    <td></td>
    <td>"MonkeyChat Bobo s'il vous plaît envoyer plus de bananes"</td>
</tr>
<tr>
    <td>"Oui, envoyer plaît di envoi messaggio s'il vous più de bananes à Bobo sur Monkeychat"</td>
    <td></td>
</tr>
</table>

Ancora un altro utente potrebbe essere molto dettagliato nella conversazione:

<table width="100%" border="1px">
<tr>
    <td width="50%"><b>Siri</b></td>
    <td width="50%"><b>Utente</b></td>
</tr>
<tr>
    <td></td>
    <td>"Hey Siri, possibile, si me venire e avviare l'app MonkeyChat per inviare un testo con il messaggio, inviare ulteriori banane"</td>
</tr>
<tr>
    <td>"A cui?"</td>
    <td></td>
</tr>
<tr>
    <td></td>
    <td>"Il migliore alla pubblicazione Bobo"</td>
</tr>
</table>

Inoltre, esistono molti modi che Siri può rispondere a una richiesta, alcuni in base alle modalità in cui è stata effettuata la richiesta:

- **Tenendo premuto il pulsante Home** -Siri verranno fornite più risposte visual con commenti verbali limitato.
- **Per "Hey Siri"** -Siri verrà più descrittiva e fornire risposte visual meno.

Siri è anche ottimizzato per soddisfare le esigenze di accessibilità dell'utente e interagire e rispondere in base a tali requisiti.

Indipendentemente dal modo in cui viene effettuata una richiesta o come Siri risponde alla richiesta, Siri gestisce la conversazione con l'utente e l'applicazione (tramite le relative estensioni) fornisce la funzionalità.

Quando l'utente effettua una richiesta di Siri verbale, ecco i passaggi successivi Siri:

[ ![](understanding-sirikit-images/monkeychat02.png "I passaggi successivi Siri")](understanding-sirikit-images/monkeychat02.png)

1. In primo luogo, Siri accetta l'audio dell'utente **vocale** e lo converte in testo.
2. Successivamente, il testo viene convertito in un **finalità**, un oggetto strutturato rappresentazione della richiesta dell'utente.
3. In base all'intento, Siri richiederà **azione** per eseguire la richiesta dell'utente.
4. Infine, in cui verranno presentate Siri **risposte** (visual e verbali) per l'utente in base l'azione eseguita.

Esistono tre modi principali che l'app può essere incluse nella conversazione dell'utente con Siri:

[ ![](understanding-sirikit-images/monkeychat03.png "I tre modi principali che l'app può partecipare alla conversazione gli utenti con Siri")](understanding-sirikit-images/monkeychat03.png)

1. **Vocabolario** -si tratta di come l'app indica Siri le parole, è necessario sapere per interagire con esso.
2. **Logica app** : queste sono le azioni e le risposte che verrà eseguita l'app in base a determinati tipi.
3. **Interfaccia utente** -si tratta dell'interfaccia utente personalizzata facoltativa che l'app consente di fornire risposte.

### <a name="example"></a>Esempio

Specificato le informazioni sopra riportate, esaminare l'interazione della conversazione seguente con l'app MonkeyChat:

<table width="100%" border="1px">
<tr>
    <td width="50%"><b>Siri</b></td>
    <td width="50%"><b>Utente</b></td>
</tr>
<tr>
    <td></td>
    <td>"Hey Siri, invia un messaggio Bobo in MonkeyChat"</td>
</tr>
<tr>
    <td>"Ciò che si desidera si dicono Bobo?"</td>
    <td></td>
</tr>
<tr>
    <td></td>
    <td>"Inviare ulteriori banane"</td>
</tr>
</table>

Il primo ruolo che accetta l'app nella conversazione è illustrare Siri vocale dell'utente:

[ ![](understanding-sirikit-images/monkeychat04.png "Aiutare a Siri di comprendere il riconoscimento vocale utenti")](understanding-sirikit-images/monkeychat04.png)

Siri non ha il nome "Bobo" nel proprio database, ma l'app, quindi ha condiviso queste informazioni con Siri tramite il relativo vocabolario. L'app consente anche Siri riconoscere che Bobo è un destinatario, perché ha specificato li a Siri come un *contatto*.

Siri sa che è necessario eseguire altre per inviare un messaggio rispetto a un destinatario, in modo controllerà rapidamente con l'estensione dell'App per vedere se un messaggio richiede il contenuto. Perché non ne MonkeyChat, Siri risponderà all'utente con la domanda: *"Ciò che si desidera si dicono Bobo?"*

Nell'esempio precedente, l'utente ha risposto, *"Inviare ulteriori banane"*, che verrà aggregare Siri in un tipo strutturato **finalità**:

[ ![](understanding-sirikit-images/monkeychat05.png "Siri verrà aggregare la risposta dell'utente in un intento strutturato")](understanding-sirikit-images/monkeychat05.png)

Lo scopo strutturato conterrà le informazioni seguenti:

- **Dominio:** messaggi
- **Scopo:** sendMessage
- **Destinatario:** Bobo
- **Il contenuto:** inviare ulteriori banane

Ogni dominio ha come set di conoscere *azioni* che può essere eseguita all'interno di essi e basato sul dominio e l'azione, zero-a-molti parametri potrebbero essere inclusi nello scopo è inviata all'app.

Lo scopo viene quindi inviato per l'estensione dell'App per l'elaborazione. Di conseguenza di elaborazione lo scopo, l'applicazione genererà un **IntentResponse** che verrà fornito con lo scopo e si includono parametri che descrivono cosa ha l'app di app con lo scopo.

Ogni IntentResponse includerà anche un **codice di risposta** che indica di Siri se l'app è stata in grado di completare la richiesta o non. Alcuni domini sono codici di risposta di errore molto specifico che possono essere inviati anche.

Infine, si includerà il IntentResponse un `NSUserActivity` (come quelle utilizzate per supportare mano Off). Il `NSUserActivity` verrà utilizzato per avviare l'app, se la risposta è necessario uscire dall'ambiente di Siri e immettere la password per completare l'operazione. 

Siri creerà automaticamente un appropriato `NSUserActivity` per avviare l'app e il prelievo in cui l'utente è stata arrestata nell'ambiente di Siri. Tuttavia, l'applicazione può visualizzare il proprio `NSUserActivity` con personalizzate, se necessario.

Dopo che l'app ha elaborato la finalità e ha restituito una risposta a Siri, quindi presenta i risultati all'utente (verbalmente e visivamente):

[ ![](understanding-sirikit-images/monkeychat06.png "I risultati presentati all'utente verbalmente sia visivamente")](understanding-sirikit-images/monkeychat06.png)

Siri ha risposta predefinita diverse interfacce utente per tutti i domini disponibili per l'app. Tuttavia, poiché MonkeyChat ha fornito un'estensione dell'interfaccia utente finalità facoltativa, viene utilizzato per presentare i risultati della conversazione all'utente nell'esempio precedente.

## <a name="the-intent-lifecycle"></a>Il ciclo di vita preventivo

Esistono tre attività principali che dovranno eseguire quando si lavora con tipi di estensione dell'App:

[ ![](understanding-sirikit-images/monkeychat07.png "Il ciclo di vita preventivo")](understanding-sirikit-images/monkeychat07.png)

1. L'app deve **risolvere** ogni parametro di un evento. Di conseguenza, l'app chiama risolvere più volte (una volta per ogni parametro), talvolta più volte per lo stesso parametro fino a quando l'app e l'utente concordano quali viene richiesto.
2. L'app deve **conferma** che può gestire lo scopo di richiesto e informare Siri il risultato previsto.
3. Infine, l'app deve **gestire** la finalità ed eseguire i passaggi per ottenere il risultato richiesto.

### <a name="the-resolve-stage"></a>La fase di risoluzione

La fase di risoluzione consente a Siri di comprendere i valori che l'utente ha fornito e assicura che l'utente deve effettivamente cosa succede quando viene elaborata la finalità dall'app. 

Questa fase fornisce anche un'opportunità per l'app influenzare il comportamento di Siri durante la conversazione con l'utente. A tale scopo, l'app fornirà un **risposta risoluzione**. Esistono un numero di risposta predefinito per i diversi tipi di dati che riconosce Siri.

La più comune risposta di risoluzione da app sarà **successo**, vale a dire l'app corrisponde alla porzione di dati specifica da un parametro (ad esempio nome utente della schermata) a un'informazione di cui è a conoscenza.

Talvolta potrebbe essere quando l'applicazione deve verificare che l'informazione corretta che noti corrisponda a una determinata richiesta. In questi casi, verrà inviato un **ConfirmationRequired** risposta per porre una domanda Sì o no all'utente, ad esempio *"Invia messaggio di Bobo più utili?"*

Potrebbero essere presenti altri casi in cui l'app richiederà all'utente di scegliere da un breve elenco di opzioni. In questo caso, si fornirà l'app un **risoluzione dell'ambiguità** risposta con un elenco di dieci due opzioni per l'utente di scegliere, ad esempio: 

```csharp
Who do you want to message?

* Bobo the Great
* Bobo Jr.
* Little Bobo
```

Siri verrà gestito dall'utente che effettua la selezione, verbalmente o tramite l'interazione con la UI di Siri e il risultato verrà inviato nuovamente all'app.

In altri casi, potrebbe non essere sufficienti informazioni per l'app risolvere il parametro o potrebbero essere presenti troppe corrispondenze di risolvere utilizzando sensibile al contesto (ad esempio 80 agli utenti con Bobo nel nome). In questo caso, l'app verrà inviato un **NeedsMoreDetails** risposta e Siri richiederà all'utente in modo più specifico.

Se l'utente non fornisce un valore che è necessario per tale processo, lo scopo, è possibile inviare un **NeedsValue** risposta richiedere all'utente per il valore di Siri.

Se l'app non supporta un valore che l'utente ha fornito per un parametro specifico, è possibile inviare il **UnsupportedWithReason** risposta per fornire un motivo per cui il valore non supportato. Siri richiederà quindi all'utente per un valore completamente nuovo e fornito loro il motivo è necessario.

Infine, utilizzare il **NotRequired** risposta per indicare che l'applicazione non richiede un valore per un determinato parametro a Siri. Se l'utente fornisce comunque uno, verrà semplicemente ignorato da Siri.

### <a name="the-confirm-stage"></a>La fase di conferma

La fase di conferma ha due scopi:

- Per indicare il risultato previsto di gestire un intento in modo che Siri può informare l'utente che si desidera venga eseguita a Siri.
- Fornisce un controllo di opportunità alcuno stato richiesto l'app potrebbe essere necessario per completare la richiesta presentata dall'utente, ad esempio la presenza di fondi sufficienti in a eseguire il pagamento richiesto.

L'app fornirà un **risposta finalità** del passaggio di conferma, che deve essere popolato con informazioni l'app abbia disponibile in modo Siri può comunicare, in modo efficace con l'utente.

In base al tipo di azione e del dominio, Siri potrebbe richiedere all'utente per la conferma, ad esempio prima di inviare un pagamento o di un interscambio di prenotazione.

### <a name="the-handle-stage"></a>La fase di Handle

La fase di gestione è la parte più importante dell'utilizzo di un intento perché è il punto in cui l'app soddisfi la richiesta dell'utente eseguendo l'attività che è stato chiesto di eseguire.

Esattamente come accade nella fase di conferma, l'applicazione deve fornire le informazioni sul risultato possibile in modo Siri può essere correlato all'utente. A volte queste informazioni verranno presentate visivamente o alcune volte Siri verrà semplicemente parlare all'utente. 

Potrebbe esserci volte quando l'app può richiedere più tempo per elaborare una determinata richiesta, ad esempio ritardi di chiamata di rete oppure se un utente in tempo reale deve soddisfare la richiesta (ad esempio, il completamento e un ordine di spedizione o guidando alla posizione dell'utente). Se Siri è in attesa di una risposta dall'applicazione, un'interfaccia utente in attesa visualizzerà all'utente in cui viene indicato che l'app è l'elaborazione della richiesta.

Idealmente, l'applicazione deve fornire una risposta a Siri entro due a tre secondi al massimo. Se l'app conosce che sta per richiedere più tempo di elaborazione di una risposta, è necessario inviare un **InProgress** codice di risposta a Siri. Siri quindi l'utente viene informato che l'app è l'elaborazione della richiesta in background e continuerà a tale scopo, anche se gli utenti lasciano l'ambiente di Siri.

## <a name="adding-sirikit-to-the-app"></a>Aggiunta di SiriKit all'App

Con SiriKit in iOS 10, Apple ha creato i due nuovi punti di estensione:

- **Estensione intenti** - fornisce Siri con il contenuto dell'app ed esegue le attività necessarie per soddisfare qualsiasi intenti supportati.
- **Estensione dell'interfaccia utente intenti** -fornisce un'interfaccia utente personalizzata che verrà visualizzata per il contenuto delle App all'interno di Siri. 

È inoltre disponibile un'API per fornire le parole e frasi a Siri di aiuto nel riconoscimento sotto forma di:

- **App vocabolario** -parole e frasi che sono comuni a tutti gli utenti dell'app.
- **Utente vocabolario** -parole e frasi che sono univoche per un utente dell'applicazione specificata.

## <a name="the-intents-extension"></a>L'estensione di tipi

L'estensione di tipi è responsabile della gestione delle interazioni tra l'applicazione e Siri principale come indicato di seguito:

[ ![](understanding-sirikit-images/intents01.png "L'estensione di tipi")](understanding-sirikit-images/intents01.png)

L'estensione finalità può supportare uno o più tipi, è compito dello sviluppatore per decidere come si desidera implementare SiriKit nell'app. Lo sviluppatore può inoltre aggiungere un'estensione finalità separato per ogni scopo che devono essere gestiti.  Ciò premesso, Apple richiede che lo sviluppatore di limitare il numero di estensioni con finalità di in modo che non sia Siri già aperto con l'app, che richiedono più memoria e tempo per gestire più processi.

Lo sviluppatore deve anche tenere presente che l'estensione finalità verranno eseguiti in background mentre Siri è attiva. Ciò consente a Siri di eseguire in modo attivo per una conversazione con l'utente durante la comunicazione comunque con l'estensione per elaborare le informazioni sulla richiesta.

## <a name="privacy-and-security-considerations"></a>Considerazioni di sicurezza e privacy

Apple ha intrapreso ideale per garantire che un utente privato informazioni siano sicure quando si lavora con Siri e di conseguenza, esistono diverse interazioni che richiedono all'utente di essere connessi nel dispositivo iOS. Ad esempio, quando si richiede un interscambio o effettuare un pagamento.

Esistono, inoltre, i comportamenti specifici che l'applicazione potrebbe essere necessario limitare per l'utente viene connesso al dispositivo. In queste situazioni, l'app può richiedere il **limitare durante il blocco** comportamento. Questa operazione viene eseguita tramite un'impostazione di `Info.plist` file.

Il Framework di autenticazione locale è disponibile per l'estensione finalità pertanto l'app può richiedere all'utente le informazioni di autenticazione aggiuntivi, anche se il dispositivo è già sbloccato.

Infine, Apple Pay è disponibile per l'estensione con finalità di in modo che l'app è possibile completare una transazione utilizzando Apple Pay e il foglio Apple Pay predefinito verrà visualizzato sopra l'interfaccia Siri.

Inoltre, desidera assicurarsi che gli utenti sa quando si inviano informazioni per un'app di terze parti 3rd e di conseguenza, l'utente Apple **deve** ad esempio il nome specifico dell'app (come specificato nel nome visualizzato di Bundle dell'app) quando effettua una richiesta.

Apple ha progettato Siri di eseguire naturale fluido conversazioni con l'utente e per questo motivo, il nome di Bundle dell'app possono essere utilizzato in molte parti del discorso, ovunque si adatta modo naturale richiesta dell'utente.

Uno dei problemi comuni che verranno eseguite dagli utenti è "verbify" nome dell'applicazione, in altre parole, assumendo il nome di app e utilizzarlo come un verbo in una richiesta. Ad esempio, *"MonkeyChat Bobo questi sono stati banane ottime."*

## <a name="the-intents-ui-extension"></a>L'estensione dell'interfaccia utente di tipi

L'estensione dell'interfaccia utente intenti l'opportunità di portare l'interfaccia utente dell'applicazione e personalizzazione di un'esperienza di Siri e rendere gli utenti a cui è connesso all'app. Con questa estensione, l'app può portare al marchio, nonché informazioni visual e altri nella trascrizione.

[ ![](understanding-sirikit-images/intents02.png "Output di esempio di estensione dell'interfaccia utente di tipi")](understanding-sirikit-images/intents02.png)

L'estensione dell'interfaccia utente intenti restituirà sempre un `UIViewController` e l'applicazione può aggiungere qualsiasi operazione all'interno di controller di visualizzazione, ad esempio che mostra le informazioni aggiuntive che vanno oltre la risposta iniziale. L'interfaccia utente intenti inoltre possibile aggiornare l'utente con lo stato di un evento di esecuzione prolungata, ad esempio la quantità più richiederà una corsa automobile per raggiungere il percorso di condivisione.

L'estensione dell'interfaccia utente intenti verrà sempre visualizzato insieme ad altro contenuto Siri, ad esempio l'icona dell'app e il nome nella parte superiore dell'interfaccia utente o, in base all'intento, pulsanti (ad esempio Send o Annulla) possono essere visualizzati nella parte inferiore.

Esistono alcuni casi in cui l'app può sostituire le informazioni che Siri sono visualizzate all'utente per impostazione predefinita, ad esempio messaggistica o viene eseguito il mapping in cui l'app può sostituire l'utilizzo predefinito con uno personalizzato per l'app.

> [!IMPORTANT]
> **Nota:** mentre è possibile aggiungere elementi interattivi, ad esempio `UIButtons` o `UITextFields` per l'estensione dell'interfaccia utente con finalità di `UIViewController`, questi non sono necessariamente consentiti come finalità dell'interfaccia utente in modalità interattiva e l'utente non sarà in grado di interagire con loro.

È totalmente facoltativo per l'applicazione fornire un'estensione dell'interfaccia utente con finalità di poiché Siri contiene un set predefinito di interfaccia utente per ogni tipo finalità. Inoltre, le interfacce dell'interfaccia utente intenti sono disponibili solo per determinati tipi che Apple ha considerato sarebbe utili all'utente.

## <a name="adding-sirikit-vocabulary"></a>Aggiunta di vocabolario SiriKit

L'ultima parte dell'implementazione SiriKit si trova all'interno dell'app, fornendo il vocabolario necessari. Molte applicazioni usano modalità univoco che descrive le informazioni dell'utente e i modi univoci che l'utente fornirà informazioni all'app.

Per questo motivo, Siri richiede l'assistenza dell'app per comprendere le parole e frasi univoche per l'app. Alcune di queste frasi faranno parte dell'app in modo che ogni utente verrà conoscere e comprendere. Ancora ad altri utenti sarà univoci per un determinato utente dell'app.

### <a name="app-specific-vocabulary"></a>App specifiche vocabolario

Il vocabolario specifico App definisce le parole e frasi specifiche che saranno noto a tutti gli utenti dell'applicazione, ad esempio tipi di veicoli o nomi allenamenti. Poiché questi elementi fanno parte dell'applicazione, vengono definiti un `AppIntentVocabulary.plist` file come parte del pacchetto di applicazione principale. Inoltre, queste parole e frasi devono essere localizzate.

Esistono diverse parti di un vocabolario `AppIntentVocabulary.plist` file:

- **App di esempio Usa** -forniscono una serie di casi di utilizzo comuni per le richieste che l'utente possa prendere dell'app. Ad esempio: *"Start accinge con MonkeyFit".*
- **I parametri** -forniscono un set di tipi di parametro non standard specifici per l'app. Ad esempio, nomi di allenamenti per l'app MonkeyFit. Si tratta di:
    - **Frase** -consente all'app di definire termini univoci per l'app. Ad esempio: il tipo di allenamenti "Bananarific" per l'app MonkeyFit. 
    - **Pronuncia** -suggerisce pronuncia a Siri come un semplice fonetica per una frase specifica. Ad esempio, "ba nana ri fico".
    - **Esempio** -fornisce un esempio di utilizzo di frase specificata nell'app. Ad esempio, *"Avviare una Bananarific in MonkeyFit"*.

Per ulteriori informazioni, vedere Apple [riferimento al formato di File vocabolario App](https://developer.apple.com/library/prerelease/content/documentation/Intents/Conceptual/SiriIntegrationGuide/CustomVocabularyKeys.html#//apple_ref/doc/uid/TP40016875-CH10-SW1).

### <a name="user-specific-vocabulary"></a>Utente specifico vocabolario

Il vocabolario specifico utente verrà fornito parole o frasi che sono univoche a singoli utenti dell'app. Questi verrà forniti in fase di esecuzione dall'app principale (non le estensioni App) come un set ordinato di condizioni di una priorità di utilizzo più significativi per gli utenti, le condizioni più importanti all'inizio dell'elenco ordinato.

Esaminare l'esempio di app MonkeyChat presentato in precedenza. MonkeyChat conserva un elenco di tutti i contatti dell'utente, che verrà utilizzato l'invio a Siri tramite il vocabolario specifico utente. Sono inoltre presenti un elenco dei contatti più recenti 10 che l'utente dispone di messaggi e ha un set di contatti Preferiti per ciascun utente. Per questo esempio, devono essere Contatti preferiti all'inizio del vocabolario specifica utente, aggiungendo i contatti recenti quindi il resto dei contatti dell'utente.

I tipi di informazioni seguenti sono supportati da vocabolario specifico di utente:

- Nomi di contatti.
- Nomi allenamenti.
- Nomi degli Album foto.
- Parole chiave foto.

Se l'applicazione si basa su iOS Address Book, l'app non dovranno eseguire alcuna azione, come queste informazioni sono già disponibili a Siri. L'applicazione deve solo fornire nomi di contatti, se l'applicazione dispone del proprio database dei contatti univoco.

Quando si progetta il vocabolario, è necessario fornire solo i valori necessari che gli utenti di sappiano e rilevante. Evitare di fornire informazioni quali numeri di telefono o indirizzi di posta elettronica.

L'applicazione deve inoltre aggiornare immediatamente Siri quando vocabolario specifico utente. Gli utenti sono abituati per la richiesta di informazioni da Siri il momento in cui è stato aggiunto al proprio dispositivo iOS. Ad esempio, se l'utente aggiunge un nuovo contatto nell'app, inviare informazioni a Siri non appena l'utente Salva.

Ancora più importante, l'app _deve_ eliminare le informazioni dal vocabolario Siri immediatamente poiché un utente potrebbe diventare falsare se tali file eliminati un'informazione ma Siri è stato ancora il riconoscimento, ore o giorni in un secondo momento.

> [!IMPORTANT]
> **Nota:** l'app deve rimuovere tutti del vocabolario utente specifico da Siri se l'utente sceglie di reimpostare l'app o se sono la disconnessione.

## <a name="sirikit-permissions"></a>Autorizzazioni SiriKit

Nella parte finale di SiriKit è incentrata autorizzazioni. Come con altre funzionalità di iOS (ad esempio, foto, fotocamera o contatti), gli utenti devono concedere l'autorizzazione esplicita per l'applicazione comunicare con Siri.

L'applicazione è in grado di fornire una stringa che definisce quali informazioni forniranno a Siri e assegnare un motivo per motivi per cui l'utente deve concedere l'accesso. 

Apple suggerisce che l'applicazione deve richiedere l'autorizzazione da parte dell'utente da utilizzare Siri la prima volta che l'utente apre l'app dopo che sono stati aggiornati a iOS 10. Si tratta in modo che gli utenti conoscono l'integrazione di Siri e possono utilizzo approvato prima di apportare la prima richiesta.

## <a name="sirikit-and-maps"></a>SiriKit e mappe

SiriKit è parte integrante di iOS e utilizza il framework intenti maggiore aggiunto iOS 10. Il framework dei tipi è stato progettato per condividere i tipi e le azioni comuni e condivise con altre parti del sistema.

Il framework intenti va oltre appena Siri integration e fornisce altre funzionalità, ad esempio l'integrazione di contatti in cui l'app può diventare il telefonia predefinita o un'app di messaggistica per i contatti specifici. Tipi forniscono anche la perfetta integrazione con CallKit per fornire agli utenti la migliore esperienza VOIP possibile.

L'app esegue il mapping in iOS 10 ha aggiunto funzionalità, ad esempio marcia condivisione in cui l'utente può prenotare un marcia direttamente all'interno di mappe dell'interfaccia utente. SiriKit fornisce un punto di estensione comuni con le mappe in modo marcia intenti di condivisione (e altri) possono essere condivisi tra Siri e mappe. 

Ciò significa che se l'app ha adottato le estensioni SiriKit, anche otterrà l'integrazione di mappe gratuitamente. 

## <a name="designing-a-great-siri-experience"></a>Progettazione di un'esperienza ottimale di Siri

Progettazione di un'esperienza utente ottimale quando si integra un'app in Siri è diversa dalla progettazione di un'interfaccia utente applicazione eccezionale. A differenza delle normali situazioni in cui l'utente interagisce con l'app direttamente dello schermo, quando si utilizza Siri vi sono più volte quando nessuna interfaccia visiva è visibile in tutti. Ad esempio, quando l'utente ha avviato la conversazione con *"Hey Siri"*.

### <a name="how-siri-helps-the-developer"></a>Come Siri consente allo sviluppatore

Quando si progettano le interazioni di un'app con Siri, l'applicazione da compilare un *interfaccia colloquiali*, vale a dire il contesto è derivato da conversazioni che si verificano Siri con l'utente per conto dell'applicazione.

In assenza di un riferimento visivo, l'utente deve tenere traccia di informazioni da presentate nella loro head. Per questo motivo, Siri Visualizza le informazioni minime necessarie per raggiungere l'attività dell'utente è che desiderano eseguire.

L'interfaccia colloquiali rispecchia le domande e le risposte dell'utente e di Siri durante la conversazione. Pertanto, è importante preoccuparsi di come Siri vengono poste delle domande e risponde durante la progettazione di questa interfaccia.

Nell'esempio seguente dell'utente che crea un messaggio, Siri può rispondere alla domanda: *"Al momento dell'invio?"* . L'utente può rispondere in molti modi diversi, ad esempio *"Invia"*, *"Annulla"* o qualcosa completamente non correlati a questa domanda. Indipendentemente dalla modalità conversazione riproduce, Siri gestirla per l'app e inviarla solo le informazioni appena sarà disponibile.

Esistono diversi modi che un utente può avviare una conversazione con Siri:

- Selezionando il dispositivo, il pulsante Home. In questa situazione Siri presenterà interfacce visual più e meno risposte verbali.
- Pronunciando *"Hey Siri"* e l'avvio di una conversazione mani disponibile. In questa situazione Siri sarà meno visual e più descrittiva.
- Utilizzo delle funzionalità di accessibilità, ad esempio bluetooth abilitata aids udito in cui l'interfaccia utente verrà personalizzato per un utente con particolari esigenze.
- Utilizzando Auto Play in cui l'utente deve mantenere la loro attenzione con stato attivo sulla Guida mantenendo distrazioni al minimo.

### <a name="how-the-developer-helps-siri"></a>Lo sviluppatore come consente a Siri

Quando si integra un'app con Siri, lo sviluppatore deve verificare l'integrazione spesso e assicurarsi che si tratta di un numero di richieste diverse chiedendo per la stessa porzione di informazioni o attività in diversi modi possibili.

Poiché nessun due persone simili di interazione utente, è fondamentale che lo sviluppatore ottiene tante tester beta diverse possibili per consentire di ottimizzare l'integrazione di Siri. Gli utenti possono richiedere informazioni o verificare le richieste in modi lo sviluppatore tuttavia mai di e questa ottimizzazione può garantire che il gruppo più ampio di utenti abbiano un'esperienza ottimale con la propria applicazione Siri.

In situazioni diverse e di ambienti di test. Avviare le conversazioni con Siri in tutte le modalità possibili garantire che le conversazioni rimangono fluido e naturale. Test nelle posizioni in cui l'utente più probabile che utilizzeranno l'app, ad esempio in un palestra talmente.

Assicurarsi che l'app fornisce tutte le informazioni necessarie per rappresentare correttamente la richiesta e il risultato all'utente di Siri. Ciò vale soprattutto quando si utilizza Siri in una situazione mani disponibile.

### <a name="siri-design-guidelines"></a>Linee guida di progettazione di Siri

Ricordarsi di Siri è che una conversazione con l'utente per conto dell'applicazione. Lo sviluppatore desidera certi che la conversazione rimane come uniformi e naturali come possibili.

Come per qualsiasi conversazione importante, lo sviluppatore deve verificare quanto segue:

- Che l'applicazione è preparata per la conversazione.
- Che l'app è in ascolto esattamente ciò che l'utente sta tentando di eseguire.
- Che l'app richiede le domande appropriate in momenti appropriati.
- Che l'applicazione risponde alla richiesta con le informazioni che l'utente sta cercando.

#### <a name="preparing-for-the-conversation"></a>Preparazione per la conversazione

La prima cosa da ricordare è che gli utenti dell'applicazione non deve essere esattamente come per lo sviluppatore. Si può provenire da diversi colori di sfondo, utilizzano lingue diverse o hanno esigenze particolari, quando si lavora con l'app.

Inoltre, poiché lo sviluppatore progettato e compilato l'app, hanno profonda conoscenza dell'app e il relativo funzionamento interno e funzioni che non disporranno di un utente tipico. Pertanto, lo sviluppatore potrebbe chiedere richiesta di Siri in modo diverso rispetto a un utente normale.

È per questo motivo è fondamentale disporre di molte persone possibile interagiscono con l'app tramite Siri. Gli utenti possono eseguire le richieste dell'applicazione tramite Siri che lo sviluppatore non ha considerato mai di o in modi che non prendere in considerazione lo sviluppatore.

#### <a name="ensure-the-app-is-a-good-listener"></a>Verificare che l'applicazione è un buon Listener

Lo sviluppatore deve assicurarsi che l'app è un buon listener e recupero di informazioni specifiche della conversazione che soddisfano le aspettative dell'utente. Ma è anche possibile potrebbe non avere fornito tutte le informazioni che l'applicazione richiede per ottenere l'attività richiesta.

Esistono diversi modi, che l'applicazione può gestire questa situazione:

- **Selezionare l'operazione predefinita per il valore mancante** , ad esempio una corsa app di condivisione potrebbe per impostazione predefinita la posizione corrente dell'utente se essi non sono state specificate in volevano di essere prelevate da.
- **Rendere un ipotizzare** -utilizza le informazioni specifiche su cui l'app ha sull'utente, l'applicazione potrebbe essere in grado di verificare e ipotizzare nelle informazioni mancanti, ad esempio l'inserimento di un numero di telefono cellulare mancano dalle informazioni di contatto dell'utente. Tuttavia, deve prestare attenzione a evitare non valido, ad esempio selezionando l'opzione più costosa e così via.
- **Prompt dei comandi per informazioni più** -l'app può avere Siri richiedere all'utente per il valore mancante. Tuttavia, la chiave qui è mantenendo le conversazioni semplice e al punto di. Gli utenti rapidamente diventerà sentono frustrati se dispongono di rispondere a diverse domande per ottenere la richiesta.
- **Gestire correttamente errate disponibili** -l'utente potrebbe fornire un valore che non era previsto l'app o che non può gestire nel contesto specificato. Verificare che l'app è correlata questa situazione all'utente in modo da rendere più semplice per poter risolvere e deselezionare.

Quando l'app viene presentato con un singolo valore in questione, il modo migliore per gestire questa situazione è di Siri chiedere conferma all'utente. Ad esempio, *"Si intendeva Bobo Ovest?"* , che possono rispondere con una risposta Sì o no.

Quando si verifica una situazione in cui le scelte possibili diversi potrebbe essere corrette per un singolo valore, risoluzione dell'ambiguità è il metodo di gestione preferiti. In questa situazione Siri può richiedere all'utente con un massimo di dieci possibili opzioni da selezionare. Ad esempio:

```csharp
Who do you want to send the message to?

* Bobo the Great!
* Bobo Jr.
* Little Bobo
```

Se ancora in questione, hanno chiesto di fornire una risposta completamente nuova, più specifica per un determinato valore di Siri.

#### <a name="request-final-confirmation"></a>Richiesta di conferma finale

Prima che l'applicazione esegua effettivamente l'attività per soddisfare la richiesta dell'utente, si verificherà Siri con l'estensione dell'App per verificare che tutto sia sul posto. Ad esempio, l'utente dispone di sufficiente money nel proprio account per effettuare il pagamento richiesto?

Inoltre, l'app deve garantire che fornisce tutte le informazioni possibili a Siri affinché possa presentarle all'utente e verificare che l'attività da eseguire soddisfi le loro aspettative.

Una volta che l'utente ha confermato la richiesta e l'app ha eseguito, le esigenze di app per più garantire che questa è fornita per tutti i risultati a Siri consentire possibile associarli all'utente.

#### <a name="responding-to-the-request"></a>Rispondere alla richiesta

Siri dispone di diverse interfacce utente predefinite per ognuno dei domini e le azioni che a conoscenza. Tuttavia, se appropriato, l'applicazione può visualizzare un'estensione dell'interfaccia utente personalizzata di finalità per migliorare l'esperienza dell'utente presentando dell'app personalizzazione e dell'interfaccia utente o altre informazioni maggiore rispetto a quella presente nella richiesta.

Ciò premesso, ritenuta deve essere utilizzato quando si progettano le interfacce personalizzate di Siri. In genere, l'utente desideri ottenere un'attività specifica eseguita più rapidamente possibile e non si desidera eseguire l'overload con le informazioni non necessarie.

Deve anche essere attenzione per assicurare che l'interfaccia utente personalizzata esegue la ricerca e risponde correttamente in tutti i dispositivi iOS diversi e gli orientamenti che l'utente potrebbe avere o utilizzare il dispositivo.

Quando appropriato, utilizzare l'API SiriKit per nascondere le informazioni ridondanti già presenti nel valore predefinito di Siri UI. Ancora, verificare che l'app ancora fornisce le informazioni in Siri in modo sia possibile presentare il verbalmente in situazioni di installazione visibile all'utente.

Potrebbero esistere situazioni in cui Siri verrà avviata l'app per soddisfare la richiesta dell'utente, ad esempio la presentazione di foto che l'utente ha richiesto. In queste situazioni, non sorprendere l'utente. Visualizzare le informazioni previste senza la necessità di passaggi intermedi o un'ulteriore intervento. Non visualizzare le informazioni o eseguire un'attività che non è previsto l'utente.

### <a name="polishing-the-design"></a>Ottimizzazione della progettazione

Esistono diverse operazioni che suggeriscono di Apple per la progettazione delle interfacce di conversazione a polacco. È in primo luogo, fornendo chiare e concisi vocabolario e usare esempi di casi a Siri.

Uno dei modi che un utente consente di individuare l'app è l'inizio di una conversazione con Siri e richiede, *"Cosa si può fare?"* Siri verranno visualizzati varie situazioni, può eseguire, tra cui app dello sviluppatore e l'eroe di esempio di utilizzo comuni disponibile tramite il relativo `plist` file.

Come scrivere i casi di utilizzo di esempio:

- Verificare che gli esempi includono il nome dell'applicazione.
- Mantenere l'esempio a breve e a punto.
- Fornire più esempi per ognuno dei tipi che supporta l'app.
- Assegnare una priorità di entrambi i tipi e gli esempi in base ai casi più comuni di utilizzo per l'app.
- Verificare che l'applicazione fornisce esempi localizzati.
- Verificare che ogni esempio funziona come previsto all'interno dell'app.
- Evitare l'indirizzamento Siri negli esempi, in modo da non includere testo, ad esempio *"Hey Siri..."*
- Evitare qualsiasi pleasantries non necessari, ad esempio *","* o *"ringraziamento"*.

Il tempo appropriato per esplorare e provare in modalità l'app può forma la conversazione a cui si verificano Siri con l'utente per suo conto. Assicurarsi di comunicare con utenti tipico durante il processo, come le interazioni con e le aspettative dell'applicazione potrebbero cambiare nel tempo.

È importante testare l'app in situazioni diverse e tutti i metodi diversi per richiamare una conversazione con Siri. Test nei percorsi reali l'utente potrebbe essere utilizzato l'app, fuori ufficio e tecnico.

Cercare di utilizzare le conversazioni con Siri (per conto dell'applicazione) fluido, naturale e "difficoltà solo". 

## <a name="summary"></a>Riepilogo

In questo articolo è illustrati i concetti chiave necessari per utilizzare SiriKit e visualizzate che può interagire con le app xamarin di fornire servizi sono accessibili all'utente mediante Siri e l'app esegue il mapping in un dispositivo iOS.




## <a name="related-links"></a>Collegamenti correlati

- [Esempio ElizaChat](https://developer.xamarin.com/samples/monotouch/ios10/ElizaChat/)
- [Guida per programmatori SiriKit](https://developer.apple.com/library/prerelease/content/documentation/Intents/Conceptual/SiriIntegrationGuide/index.html)
- [Tipi di riferimento di Framework](https://developer.apple.com/reference/intents)
- [Tipi di riferimento di Framework dell'interfaccia utente](https://developer.apple.com/reference/intentsui)
