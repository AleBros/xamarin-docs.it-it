---
title: Informazioni sui concetti di SiriKit
description: Questo documento descrive i concetti chiave necessari per l'uso di SiriKit in un'app Novell. iOS. Ad esempio, vengono illustrati gli Intent e le estensioni dell'interfaccia utente, le autorizzazioni per SiriKit, la progettazione di un'esperienza ottimale e altro ancora.
ms.prod: xamarin
ms.assetid: 99EC5C1E-484F-4371-8555-58C9F60DE37F
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 05/02/2017
ms.openlocfilehash: bce2c1e543084ea80908946b1e37e43cf53c1676
ms.sourcegitcommit: c9651cad80c2865bc628349d30e82721c01ddb4a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/03/2019
ms.locfileid: "70227355"
---
# <a name="understanding-sirikit-concepts"></a>Informazioni sui concetti di SiriKit

_Questo articolo illustra i concetti chiave che saranno necessari per l'uso di SiriKit in un'app Novell. iOS._


Una novità di iOS 10, SiriKit consente a un'app Novell. iOS di fornire servizi accessibili all'utente tramite Siri e l'app Maps in un dispositivo iOS. Questa funzionalità è disponibile in una o più estensioni di app usando i nuovi framework **dell'interfaccia utente** Intent e Intent.

SiriKit consente a un'app per iOS di fornire servizi accessibili all'utente usando Siri e l'app Maps in un dispositivo iOS usando le estensioni delle app e i nuovi framework **dell'interfaccia utente** Intent e Intent.

Siri funziona con il concetto di **domini**, gruppi di azioni di conoscenza per le attività correlate. Ogni interazione eseguita dall'app con Siri deve rientrare in uno dei relativi domini di servizio noti, come indicato di seguito:

- Chiamata audio o video.
- Prenotare una corsa.
- Gestione degli allenamenti.
- Messaggistica.
- Ricerca di foto.
- Invio o ricezione dei pagamenti.

Quando l'utente effettua una richiesta di Siri che interessa uno dei servizi dell'estensione dell'app, SiriKit invia all'estensione un oggetto **preventivo** che descrive la richiesta dell'utente insieme a tutti i dati di supporto. L'estensione dell'app genera quindi l'oggetto **Response** appropriato per la **finalità**specificata, in cui viene illustrato in dettaglio il modo in cui l'estensione può gestire la richiesta.

## <a name="the-intents-and-intents-ui-extensions"></a>Estensioni dell'interfaccia utente Intent e Intent

Siri e l'app Maps interagiscono con i servizi dell'app tramite due tipi diversi di estensioni dell'app:

- **Estensione** per Intent: fornisce Siri ed esegue il mapping con il contenuto dell'app ed esegue le attività necessarie per soddisfare gli Intent supportati.
- **Estensione per interfaccia utente Intent** : fornisce un'interfaccia utente personalizzata che verrà visualizzata per il contenuto dell'app all'interno di Siri o maps.

L'app deve fornire un'estensione per gli Intent per supportare SiriKit ed è responsabile della fornitura di informazioni che Siri e Maps possono presentare all'utente e per la gestione di Intent.

La creazione di un'estensione per interfaccia utente Intent è facoltativa poiché Siri gestisce in genere tutte le interazioni dell'utente e dispone di un'interfaccia utente incorporata standard per la presentazione di informazioni in ognuno dei domini supportati. Fornendo un'estensione per interfaccia utente Intent, l'app può usare il Framework **Intent UI** per presentare un'interfaccia utente personalizzata avanzata con la personalizzazione dell'app e informazioni aggiuntive.

## <a name="siri-and-the-maps-app-role"></a>Siri e il ruolo app Maps

Le richieste pronunciate dall'utente sono la lingua elaborata e analizzata semanticamente da Siri, che consente di trasformare tali richieste in Intent che possono essere gestiti dalle estensioni Intent.

Maps usa le estensioni per finalità dell'app per visualizzare le informazioni nell'interfaccia della mappa in risposta alle azioni dell'utente. Come richiedere ristoranti vicini o ottenere le recensioni del ristorante dell'app.

Siri e Maps gestiscono tutte le interazioni dell'utente e visualizzano i risultati usando l'interfaccia di sistema standard. Il ruolo estensioni app è principalmente per fornire i dati che vengono visualizzati. Facoltativamente, l'app può fornire un'estensione per interfaccia utente Intent e presentare un'interfaccia utente personalizzata per migliorare l'interfaccia di sistema predefinita.

## <a name="interacting-with-siri-via-sirikit"></a>Interazione con Siri tramite SiriKit

Questa sezione presenta una panoramica del modo in cui SiriKit consente all'utente di interagire con l'app usando Siri. Ai fini di questo esempio, si userà l'app Fake MonkeyChat:

[![](understanding-sirikit-images/monkeychat01.png "Icona MonkeyChat")](understanding-sirikit-images/monkeychat01.png#lightbox)

MonkeyChat mantiene il proprio libro di contatto degli amici dell'utente, ognuno associato a un nome di schermata (ad esempio, Bobo) e consente all'utente di inviare chat di testo a ogni amico con il nome dello schermo.

Ci sono molti modi in cui l'utente può avviare un'interazione con l'app, perché persone diverse possono eseguire la stessa richiesta in molte forme diverse.

Ad esempio, se l'utente vuole inviare un messaggio al proprio amico Bobo, potrebbe avere la conversazione seguente con Siri:

_Utente Hey Siri, invia un messaggio MonkeyChat._<br />
_Siri A chi?_<br />
_Utente Bobo._<br />
_Siri Che cosa si vuole dire a Bobo?_<br />
_Utente Invia altre banane._<br />

Un altro utente potrebbe effettuare la stessa richiesta con una conversazione diversa:

_Utente Inviare un messaggio a Bobo in MonkeyChat._<br />
_Siri Che cosa si vuole dire a Bobo?_<br />
_Utente Invia altre banane._<br />

Un altro utente potrebbe effettuare una richiesta ancora più breve:

_Utente MonkeyChat Bobo invia altre banane._<br />
_Siri Ok, l'invio di un messaggio invia altre banane a Bobo in Monkeychat._<br />

O anche effettuare la stessa richiesta in una lingua diversa:

_Utente MonkeyChat Bobo s'vous plaît emissario Plus de banane._<br />
_Siri Oui, envoi message s'vous plaît inviator Plus de bananes à Bobo sur Monkeychat._<br />

Tuttavia, un altro utente potrebbe essere molto dettagliato nella conversazione:

_Utente Salve Siri, è possibile fare un favore e avviare l'app MonkeyChat per inviare un SMS con il messaggio. si consiglia di inviare altre banane._<br />
_Siri A chi?_<br />
_Utente Il mio migliore amico Bobo._<br />

Inoltre, è possibile che Siri risponda a una richiesta, altre in base al modo in cui è stata effettuata la richiesta:

- **Tenendo premuto il pulsante Home** , Siri fornirà ulteriori risposte visive con feedback verbale limitato.
- **Per "Hey Siri"** -Siri sarà più verbale e fornirà un minor numero di risposte visive.

Siri è inoltre ottimizzato per soddisfare le esigenze di accessibilità dell'utente e interagirà e risponderà in base a tali esigenze.

Indipendentemente da come viene effettuata una richiesta o dal modo in cui Siri risponde alla richiesta, Siri gestisce la conversazione con l'utente e l'app (tramite le relative estensioni) fornisce la funzionalità.

Quando l'utente effettua una richiesta verbale di Siri, questi sono i passaggi che Siri seguiranno:

[![](understanding-sirikit-images/monkeychat02.png "Passaggi che Siri seguiranno")](understanding-sirikit-images/monkeychat02.png#lightbox)

1. Per prima cosa, Siri prende l'audio della **voce** dell'utente e lo converte in testo.
2. Successivamente, il testo viene convertito in un **Intent**, una rappresentazione strutturata della richiesta dell'utente.
3. In base allo scopo, Siri interviene per eseguire la richiesta dell'utente.
4. Infine, Siri presenta le **risposte** (sia visive che verbali) per l'utente in base all'azione eseguita.

Esistono tre modi principali in cui l'app può partecipare alla conversazione dell'utente con Siri:

[![](understanding-sirikit-images/monkeychat03.png "I tre modi principali in cui l'app può partecipare alla conversazione degli utenti con Siri")](understanding-sirikit-images/monkeychat03.png#lightbox)

1. **Vocabolario** : questo è il modo in cui l'app indica a Siri le parole che devono sapere per interagire con esso.
2. **Logica dell'app** : queste sono le azioni e le risposte che l'app prenderà in base a determinati Intent.
3. **Interfaccia utente** : questa è l'interfaccia utente personalizzata facoltativa a cui l'app può fornire le risposte.

### <a name="example"></a>Esempio

Date le informazioni sopra riportate, esaminare il modo in cui la conversazione seguente interagirà con l'app MonkeyChat:

_Utente Hey Siri, inviare un messaggio a Bobo in MonkeyChat._<br />
_Siri Che cosa si vuole dire a Bobo?_<br />
_Utente Invia altre banane._<br />

Il primo ruolo che l'app prende in discussione consiste nel consentire a Siri di comprendere la voce dell'utente:

[![](understanding-sirikit-images/monkeychat04.png "Aiutare Siri a comprendere il discorso degli utenti")](understanding-sirikit-images/monkeychat04.png#lightbox)

Siri non ha il nome "Bobo" nel database, ma l'app esegue e condivide queste informazioni con Siri tramite il vocabolario. L'app aiuta anche Siri a riconoscere che Bobo è un destinatario, perché lo ha specificato in Siri come *contatto*.

Siri sa che è necessario più di inviare un messaggio rispetto a un solo destinatario, quindi verificherà rapidamente con l'estensione dell'app per verificare se un messaggio richiede contenuto. Poiché MonkeyChat lo fa, Siri risponderà all'utente con la domanda: *"Cosa vuoi dire a Bobo?"*

Nell'esempio precedente l'utente ha risposto: *"inviare più banane"* , che Siri includerà un intento strutturato:

[![](understanding-sirikit-images/monkeychat05.png "Siri raggruppa la risposta dell'utente in un intento strutturato")](understanding-sirikit-images/monkeychat05.png#lightbox)

L'intento strutturato conterrà le informazioni seguenti:

- **Dominio** Messages
- **Finalità:** SendMessage
- **Destinatario** Bobo
- **Contenuto:** Invia altre banane

Ogni dominio dispone di un set di *azioni* che possono essere eseguite all'interno di esse e in base al dominio e all'azione. i parametri da zero a molti potrebbero essere inclusi nell'intento inviato all'app.

Lo scopo viene quindi inviato all'estensione dell'app per l'elaborazione. In seguito all'elaborazione dello scopo, l'app genererà un **IntentResponse** che verrà aggregato con lo scopo e i parametri di inclusione che descrivono le finalità dell'applicazione.

Ogni IntentResponse includerà anche un **codice di risposta** che indica a Siri se l'app è stata in grado di completare la richiesta. Alcuni domini presentano anche codici di risposta di errore molto specifici che possono essere inviati.

Infine, IntentResponse includerà un ( `NSUserActivity` come quelle usate per supportare la consegna). `NSUserActivity` Verrà usato per avviare l'app se la risposta richiede che lasci l'ambiente Siri e immetta l'app per il completamento.

Siri compilerà automaticamente un `NSUserActivity` appropriato per avviare l'app e il pickup da cui l'utente ha lasciato l'ambiente Siri. Tuttavia, l'app può fornire le proprie `NSUserActivity` informazioni personalizzate, se necessario.

Dopo che l'app ha elaborato lo scopo e ha restituito una risposta a Siri, presenta i risultati all'utente (sia verbalmente che visivamente):

[![](understanding-sirikit-images/monkeychat06.png "I risultati presentati all'utente in modo verbale e visivo")](understanding-sirikit-images/monkeychat06.png#lightbox)

Siri include diverse interfacce utente di risposta predefinite per ognuno dei domini disponibili per l'app. Tuttavia, poiché MonkeyChat ha fornito un'estensione facoltativa dell'interfaccia utente Intent, viene usata per presentare i risultati della conversazione all'utente nell'esempio precedente.

## <a name="the-intent-lifecycle"></a>Ciclo di vita preventivo

Ci sono tre attività principali che l'estensione dell'app deve eseguire quando si gestiscono gli Intent:

[![](understanding-sirikit-images/monkeychat07.png "Ciclo di vita preventivo")](understanding-sirikit-images/monkeychat07.png#lightbox)

1. L'app deve **risolvere** ogni parametro in un evento. Di conseguenza, l'app chiamerà Resolve più volte (una volta per ogni parametro) e talvolta più volte sullo stesso parametro fino a quando l'app e l'utente non concordano sugli elementi richiesti.
2. L'app deve **verificare** che sia in grado di gestire la finalità richiesta e indicare a Siri il risultato previsto.
3. Infine, l'app deve **gestire** lo scopo ed eseguire i passaggi per ottenere il risultato richiesto.

### <a name="the-resolve-stage"></a>Fase di risoluzione

La fase di risoluzione consente a Siri di comprendere i valori forniti dall'utente e garantisce che ciò che l'utente ha effettivamente significato è quello che accadrà quando lo scopo verrà elaborato dall'app.

Questa fase offre anche l'opportunità per l'app di influenzare il comportamento di Siri durante la conversazione con l'utente. A tale scopo, l'app fornirà una **risposta di risoluzione**. È disponibile una serie di risposte predefinite ai diversi tipi di dati che Siri riconosce.

La risposta più comune da parte dell'app avrà **esito positivo**, ovvero l'app corrisponderebbe ai dati specifici di un parametro (ad esempio, il nome della schermata utente) a una parte di informazioni che conosce.

In alcuni casi l'app deve verificare che una determinata richiesta corrisponda alle informazioni corrette. In questi casi, invierà una risposta **ConfirmationRequired** per porre una domanda affermativa o no all'utente, ad esempio *"Send Message to Bobo The Great?"*

Potrebbero essere presenti altri casi in cui l'app richiederà all'utente di scegliere un breve elenco di opzioni. In questo caso, l'app fornirà una risposta di risoluzione dell'ambiguità con un elenco di due o dieci opzioni che l'utente può scegliere, ad esempio:

```csharp
Who do you want to message?

* Bobo the Great
* Bobo Jr.
* Little Bobo
```

Siri gestirà l'utente effettuando la selezione, in modo verbale o interagendo con l'interfaccia utente di Siri, e il risultato verrà restituito all'app.

In altri casi, potrebbero non essere disponibili informazioni sufficienti per consentire all'app di risolvere il parametro o potrebbero essere presenti troppe corrispondenze da risolvere usando la risoluzione dell'ambiguità (ad esempio, gli utenti 80 con Bobo nel nome). In questi casi, l'app invierà una risposta **NeedsMoreDetails** e Siri chiederà all'utente di essere più specifico.

Se l'utente non ha fornito un valore necessario per l'elaborazione dello scopo, può inviare una risposta **NeedsValue** per fare in modo che Siri chieda all'utente il valore.

Se l'app non supporta un valore specificato dall'utente per un parametro specifico, può inviare la risposta **UnsupportedWithReason** per fornire un motivo per cui il valore non è supportato. Siri chiederà all'utente di specificare un valore completamente nuovo e di specificare il motivo per cui è necessario.

Usare infine la risposta **NotRequired** per indicare a Siri che l'app non richiede un valore per un determinato parametro. Se l'utente ne fornisce comunque uno, verrà semplicemente ignorato da Siri.

### <a name="the-confirm-stage"></a>Fase di conferma

La fase di conferma ha due scopi:

- Per indicare a Siri il risultato previsto della gestione di un Intent in modo che Siri possa indicare all'utente cosa succederà.
- Consente di controllare tutti gli stati richiesti che l'app potrebbe dover completare la richiesta presentata dall'utente, ad esempio la presenza di un numero sufficiente di denaro nella banca per effettuare il pagamento richiesto.

L'app fornirà una **risposta Intent** dal passaggio di conferma, che dovrebbe essere popolata con la quantità di informazioni disponibile per l'app, in modo che Siri possa comunicare efficacemente con l'utente.

In base al dominio e al tipo di azione, Siri potrebbe richiedere conferma all'utente, ad esempio prima di inviare un pagamento o di prenotare una corsa.

### <a name="the-handle-stage"></a>Fase dell'handle

La fase di gestione è la parte più importante dell'uso di un Intent perché è il punto in cui l'app soddisfa la richiesta dell'utente eseguendo l'attività a cui è stato richiesto.

Analogamente alla fase di conferma, l'app deve fornire quante più informazioni sul risultato possibile, in modo che Siri possa correlare questa operazione all'utente. A volte queste informazioni vengono presentate in modo visivo o in altro modo Siri lo parlerà di nuovo all'utente.

In alcuni casi è possibile che l'app richieda più tempo per elaborare una determinata richiesta, ad esempio ritardi di chiamata di rete o se una persona attiva deve soddisfare la richiesta, ad esempio il completamento e la spedizione di un ordine o l'indirizzamento di un'automobile alla posizione dell'utente. Quando Siri è in attesa di una risposta dall'app, visualizzerà un'interfaccia utente in attesa per informare l'utente che l'app sta elaborando la richiesta.

Idealmente, l'app deve fornire una risposta a Siri entro due o tre secondi al massimo. Se l'app sa che una determinata risposta richiederà più tempo per l'elaborazione, deve inviare un codice di risposta in **corso** a Siri. Siri indicherà all'utente che l'app sta elaborando la richiesta in background e continuerà a farlo anche se lascia l'ambiente Siri.

## <a name="adding-sirikit-to-the-app"></a>Aggiunta di SiriKit all'app

Con SiriKit in iOS 10, Apple ha creato due nuovi punti di estensione:

- **Estensione** per Intent: fornisce Siri con il contenuto dell'app ed esegue le attività necessarie per soddisfare gli Intent supportati.
- **Estensione per interfaccia utente Intent** : fornisce un'interfaccia utente personalizzata che verrà visualizzata per il contenuto delle app all'interno di Siri.

È inoltre disponibile un'API per fornire parole e frasi a Siri per facilitare il riconoscimento nel formato:

- **Vocabolario delle app** : parole e frasi comuni a tutti gli utenti dell'app.
- **Vocabolario utente** : parole e frasi univoche per un determinato utente dell'app.

## <a name="the-intents-extension"></a>Estensione per Intent

L'estensione per Intent è responsabile della gestione delle interazioni principali tra l'app e Siri come indicato di seguito:

[![](understanding-sirikit-images/intents01.png "Estensione per Intent")](understanding-sirikit-images/intents01.png#lightbox)

L'estensione per finalità può supportare uno o più Intent, spetta allo sviluppatore decidere come implementare SiriKit nell'app. Lo sviluppatore può anche aggiungere un'estensione per finalità separate per ogni finalità che deve essere gestita.  Detto questo, Apple richiede che lo sviluppatore limiti il numero di estensioni per finalità in modo che Siri non abbia più processi aperti sull'app, che richiedono più memoria e tempo per gestire.

Lo sviluppatore deve inoltre tenere presente che l'estensione dello scopo verrà eseguita in background, mentre Siri è attiva. Questo consente a Siri di eseguire attivamente una conversazione con l'utente comunicando con l'estensione per elaborare le informazioni sulla richiesta.

## <a name="privacy-and-security-considerations"></a>Considerazioni sulla privacy e sulla sicurezza

Apple ha adottato misure eccezionali per assicurarsi che le informazioni private di un utente siano sicure quando si lavora con Siri e, di conseguenza, esistono diverse interazioni che richiedono che l'utente sia connesso al dispositivo iOS. Ad esempio, quando si richiede una corsa o si effettua un pagamento.

Inoltre, esistono comportamenti specifici che l'app potrebbe voler limitare all'utente che ha eseguito l'accesso al dispositivo. Per queste situazioni, l'app può richiedere la **limitazione quando** il comportamento è bloccato. Questa operazione viene eseguita tramite un'impostazione nel `Info.plist` file.

Il Framework di autenticazione locale è disponibile per l'estensione Intent, in modo che l'app possa chiedere all'utente informazioni aggiuntive sull'autenticazione, anche se il dispositivo è già sbloccato.

Infine, Apple Pay è disponibile per l'estensione Intent, in modo che l'app possa completare una transazione utilizzando Apple Pay e il foglio di Apple Pay incorporato verrà visualizzato sopra l'interfaccia Siri.

Apple vuole inoltre assicurarsi che gli utenti sappiano quando inviano informazioni a un'app di terze parti e, di conseguenza, l'utente **deve** pronunciare il nome specifico dell'app (come specificato nel nome visualizzato del bundle dell'app) quando si effettua una richiesta.

Apple ha progettato Siri per realizzare conversazioni naturali e fluide con l'utente. per questo motivo, il nome del bundle dell'app può essere usato in molte parti del discorso, ovunque si trovi in modo naturale nella richiesta dell'utente.

Una delle cose comuni che gli utenti faranno è "verbify" il nome dell'app, in altre parole, prendendo il nome dell'app e usandolo come verbo in una richiesta. Ad esempio, *"MonkeyChat Bobo erano belle banane".*

## <a name="the-intents-ui-extension"></a>Estensione per interfaccia utente Intent

L'estensione per interfaccia utente Intent presenta la possibilità di portare l'interfaccia utente e la personalizzazione dell'app nell'esperienza Siri e di fare in modo che gli utenti si sentano connessi all'app. Con questa estensione, l'app può portare il marchio, nonché informazioni visive e altre informazioni nella trascrizione.

[![](understanding-sirikit-images/intents02.png "Output dell'estensione dell'interfaccia utente di esempio")](understanding-sirikit-images/intents02.png#lightbox)

L'estensione dell'interfaccia utente Intent restituirà sempre `UIViewController` un oggetto e l'app può aggiungere qualsiasi elemento che mi piace all'interno del controller di visualizzazione, ad esempio la visualizzazione di informazioni aggiuntive che vanno oltre la risposta iniziale. L'interfaccia utente di Intent può anche aggiornare l'utente con lo stato di un evento a esecuzione prolungata, ad esempio il tempo necessario per l'esecuzione di un'automobile di condivisione della corsa per raggiungere la loro posizione.

L'estensione dell'interfaccia utente Intent verrà sempre visualizzata insieme ad altri contenuti Siri, ad esempio l'icona e il nome dell'app nella parte superiore dell'interfaccia utente o, in base allo scopo, i pulsanti (ad esempio Send o Cancel) potrebbero essere visualizzati in basso.

Esistono alcuni casi in cui l'app può sostituire le informazioni che Siri Visualizza all'utente per impostazione predefinita, ad esempio la messaggistica o le mappe in cui l'app può sostituire l'esperienza predefinita con una personalizzata per l'app.

> [!IMPORTANT]
> Sebbene sia possibile aggiungere elementi interattivi `UIButtons` `UIViewController`, ad esempio o `UITextFields` , all'estensione dell'interfaccia utente Intent, questi sono rigorosamente proibiti come l'interfaccia utente Intent in non interattivo e l'utente non sarà in grado di interagire con essi.

È assolutamente facoltativo per l'app fornire un'estensione dell'interfaccia utente Intent perché Siri contiene un set predefinito di interfaccia utente per ogni tipo di Intent. Inoltre, le interfacce dell'interfaccia utente Intent sono disponibili solo per determinati Intent che Apple ha ritenuto utile all'utente.

## <a name="adding-sirikit-vocabulary"></a>Aggiunta del vocabolario SiriKit

L'ultima parte dell'implementazione di SiriKit si trova all'interno dell'app fornendo il vocabolario richiesto. Molte app hanno modi univoci per descrivere le informazioni all'utente e le modalità univoche che l'utente fornirà informazioni all'app.

Per questo motivo, Siri richiede l'assistenza dell'app per comprendere le parole e le frasi univoche per l'app. Alcune di queste frasi faranno parte dell'app in modo che ogni utente possa conoscerle e comprenderle. Tuttavia, altri saranno univoci per un determinato utente dell'app.

### <a name="app-specific-vocabulary"></a>Vocabolario specifico dell'app

Il vocabolario specifico dell'app definisce le parole e le frasi specifiche che saranno note a tutti gli utenti dell'app, ad esempio i tipi di veicolo o i nomi degli allenamenti. Poiché queste sono parte dell'applicazione, sono definite in un `AppIntentVocabulary.plist` file come parte del bundle principale dell'app. Inoltre, le parole e le frasi devono essere localizzate.

Sono disponibili diverse parti per un file `AppIntentVocabulary.plist` di vocabolario:

- **Esempi** di utilizzo dell'app: questi forniscono un set di casi d'uso comuni per le richieste che l'utente può creare dell'app. Ad esempio:  *"Avviare un allenamento con MonkeyFit".*
- **Parameters** : forniscono un set di tipi di parametro non standard specifici dell'app. Ad esempio, i nomi degli allenamenti per l'app MonkeyFit. Sono costituiti da:
  - **Frase** : consente all'app di definire termini univoci per l'app. Ad esempio, il tipo di allenamento "Bananarific" per l'app MonkeyFit.
  - **Pronuncia** : fornisce gli hint di pronuncia a Siri come semplice ortografia fonetica per una frase specifica. Ad esempio, "BA Nana ri FIC".
  - **Esempio** : viene fornito un esempio di uso della frase specificata nell'app. Ad esempio, *"avviare un Bananarific in MonkeyFit"* .

Per altre informazioni, vedere il riferimento al [formato di file del vocabolario app](https://developer.apple.com/library/prerelease/content/documentation/Intents/Conceptual/SiriIntegrationGuide/CustomVocabularyKeys.html#//apple_ref/doc/uid/TP40016875-CH10-SW1)di Apple.

### <a name="user-specific-vocabulary"></a>Vocabolario specifico dell'utente

Il vocabolario specifico dell'utente fornirà parole o frasi univoche per i singoli utenti dell'app. Questi verranno forniti in fase di esecuzione dall'app principale (non dalle estensioni dell'app) come un set ordinato di termini ordinati in una priorità di utilizzo più significativa per gli utenti, con le condizioni più importanti all'inizio dell'elenco.

Esaminare l'esempio dell'app MonkeyChat illustrata in precedenza. MonkeyChat mantiene un elenco di tutti i contatti dell'utente che invierà a Siri tramite il vocabolario specifico dell'utente. Consente inoltre di mantenere un elenco dei 10 contatti più recenti di cui l'utente ha creato un messaggio ed è dotato di un set di contatti preferiti per ogni utente. Per questo esempio, i contatti preferiti devono trovarsi all'inizio del vocabolario specifico dell'utente, seguito dai contatti recenti e quindi dal resto dei contatti dell'utente.

Il vocabolario specifico dell'utente supporta i tipi di informazioni seguenti:

- Nomi dei contatti.
- Nomi degli allenamenti.
- Nomi degli album di foto.
- Parole chiave foto.

Se l'app si basa sulla rubrica iOS, l'app non deve eseguire alcuna azione, perché queste informazioni sono già disponibili per Siri. L'app deve solo fornire nomi di contatto se l'app dispone di un proprio database univoco di contatti.

Quando si progetta il vocabolario, fornire solo i valori necessari a cui gli utenti sono interessati. Evitare di fornire informazioni come numeri di telefono o indirizzi di posta elettronica.

L'app deve anche aggiornare Siri prontamente quando cambia il vocabolario specifico dell'utente. Gli utenti sono abituati a richiedere informazioni da Siri nel momento in cui sono state aggiunte al dispositivo iOS. Ad esempio, se l'utente aggiunge un nuovo contatto nell'app, inviare le informazioni a Siri non appena l'utente lo salva.

Ancora più importante, l'app _deve_ eliminare tempestivamente le informazioni dal vocabolario Siri perché un utente potrebbe essere sconvolto se ha eliminato una parte di informazioni, ma Siri è ancora in grado di riconoscere ore o giorni successivi.

> [!IMPORTANT]
> L'app deve rimuovere tutto il vocabolario specifico dell'utente da Siri se l'utente sceglie di reimpostare l'app o se si scollega.

## <a name="sirikit-permissions"></a>Autorizzazioni SiriKit

L'ultima parte di SiriKit è incentrata sulle autorizzazioni. Analogamente all'uso di altre funzionalità di iOS (ad esempio foto, fotocamera o contatti), gli utenti devono concedere esplicitamente l'autorizzazione all'app per comunicare con Siri.

L'app è in grado di fornire una stringa che definisce le informazioni che fornirà a Siri e giustifica il motivo per cui l'utente deve concedere questo accesso.

Apple suggerisce che l'app deve richiedere l'autorizzazione all'utente per usare Siri la prima volta che l'utente apre l'app dopo aver eseguito l'aggiornamento a iOS 10. Questo è il modo in cui gli utenti conoscono l'integrazione con Siri e possono preapprovare l'utilizzo prima di effettuare la prima richiesta.

## <a name="sirikit-and-maps"></a>SiriKit e mappe

SiriKit è parte integrante di iOS e usa il Framework di Intent più ampio aggiunto ad iOS 10. Il Framework Intent è stato progettato per condividere azioni comuni e condivise e Intent con altre parti del sistema.

Il Framework Intent va oltre la semplice integrazione con Siri e fornisce altre funzionalità, ad esempio l'integrazione dei contatti, in cui l'app può diventare la telefonia predefinita o l'app di messaggistica per contatti specifici. Gli Intent forniscono anche una profonda integrazione con CallKit per offrire agli utenti la migliore esperienza VOIP possibile.

L'app Maps in iOS 10 ha aggiunto funzionalità, ad esempio la condivisione dei viaggi in cui l'utente può prenotare una corsa direttamente all'interno dell'interfaccia utente maps. SiriKit fornisce un punto di estensione comune con le mappe, in modo da consentire la condivisione dei viaggi (e altri) tra Siri e Maps.

Ciò significa che se l'app ha adottato le estensioni SiriKit, otterrà anche l'integrazione di Maps gratuitamente.

## <a name="designing-a-great-siri-experience"></a>Progettazione di una grande esperienza Siri

La progettazione di un'esperienza utente ottimale quando si integra un'app in Siri è diversa dalla progettazione di un'interfaccia utente di app ottimale. Diversamente dalle situazioni normali in cui l'utente interagisce con l'app direttamente sullo schermo, quando si usa Siri ci sono molte volte quando non è visibile alcuna interfaccia visiva. Ad esempio, quando l'utente ha iniziato la conversazione con *"Hey Siri"* .

### <a name="how-siri-helps-the-developer"></a>Come Siri aiuta lo sviluppatore

Quando si progettano le interazioni di un'app con Siri, l'app creerà un' *interfaccia colloquiale*, ovvero il contesto viene derivato dalla conversazione che Siri ha con l'utente per conto dell'app.

In assenza di un riferimento visivo, l'utente deve tenere traccia delle informazioni presentate nella propria intestazione. Per questo motivo, Siri presenta le informazioni minime necessarie per ottenere l'attività che l'utente desidera eseguire.

L'interfaccia conversazione viene modellata in base alle domande e alle risposte dell'utente e di Siri durante la conversazione. È quindi importante considerare il modo in cui Siri pone domande e risponde quando si progetta questa interfaccia.

Prendere l'esempio seguente di creazione di un messaggio, Siri potrebbe rispondere con la domanda *"pronto per l'invio?"* . L'utente può rispondere in molti modi diversi, ad esempio *"Invia it"* , *"Annulla"* o anche qualcosa di totalmente non correlato a questa domanda. Indipendentemente dal modo in cui la conversazione viene riprodotta, Siri la gestirà per l'app e le invierà solo le informazioni rilevanti non appena diventano disponibili.

È possibile che un utente avvii una conversazione con Siri in diversi modi:

- Selezionando il dispositivo, premere il pulsante Home. In questa situazione Siri presenta più interfacce visive e meno risposte verbali.
- Pronunciando *"Hey Siri"* e iniziando una conversazione di hands free. In questa situazione Siri risulteranno meno visivi e più verbali.
- Uso delle funzionalità di accessibilità, ad esempio la tecnologia Bluetooth con supporto acustico, in cui l'interfaccia utente sarà personalizzata per un utente con esigenze particolari.
- Uso dell'auto play in cui l'utente deve tenere l'attenzione sulla guida mantenendo al minimo le distrazioni.

### <a name="how-the-developer-helps-siri"></a>Come lo sviluppatore aiuta Siri

Quando si integra un'app con Siri, lo sviluppatore deve testare questa integrazione spesso e assicurarsi che vengano effettuate molte richieste diverse richiedendo la stessa informazione o attività in tutti i modi possibili.

Poiché due persone non pensano allo stesso modo, è fondamentale che lo sviluppatore ottenga il maggior numero possibile di tester beta per ottimizzare l'integrazione con Siri. Gli utenti possono richiedere informazioni o inviare richieste in modo che lo sviluppatore non sia mai stato e questa ottimizzazione può contribuire a garantire che il più ampio gruppo di utenti abbia un'esperienza ottimale nell'uso dell'app con Siri.

Eseguire test in situazioni e ambienti diversi. Inizia le conversazioni con Siri in tutti i modi possibili per garantire che queste conversazioni siano sempre fluide e naturali. Eseguire test in posizioni in cui l'utente potrebbe usare più probabilmente l'app, ad esempio in una palestra affollata.

Assicurarsi che l'app fornisca tutte le informazioni necessarie a Siri per rappresentare correttamente la richiesta e il risultato per l'utente. Questo vale soprattutto quando si usa Siri in una situazione gratuita.

### <a name="siri-design-guidelines"></a>Linee guida per la progettazione di Siri

Tenere sempre presente che Siri ha una conversazione con l'utente per conto dell'app. Lo sviluppatore vuole non assicurarsi che questa conversazione rimanga più fluida e naturale possibile.

Come per qualsiasi conversazione importante, lo sviluppatore deve garantire quanto segue:

- Che l'applicazione sia preparata per la conversazione.
- Che l'app ascolti esattamente ciò che l'utente sta tentando di eseguire.
- Che l'app chieda le domande appropriate nei momenti appropriati.
- Che l'app risponda alla richiesta con le informazioni che l'utente sta cercando.

#### <a name="preparing-for-the-conversation"></a>Preparazione per la conversazione

La prima cosa da ricordare è che gli utenti dell'app non saranno esattamente come lo sviluppatore. Possono provenire da diversi background, parlano lingue diverse o hanno esigenze particolari quando si lavora con l'app.

Inoltre, dal momento che lo sviluppatore ha progettato e creato l'app, ha una conoscenza approfondita dell'app e dei suoi lavori interni e delle funzioni che un utente tipico non avrà. Quindi, lo sviluppatore potrebbe richiedere la richiesta di Siri in modo diverso rispetto a un utente normale.

Per questo motivo è fondamentale avere il maggior numero possibile di persone diverse interagire con l'app tramite Siri. Gli utenti possono effettuare richieste dell'app tramite Siri che lo sviluppatore non ha mai pensato o in modo che lo sviluppatore non ha considerato.

#### <a name="ensure-the-app-is-a-good-listener"></a>Verificare che l'app sia un listener valido

Lo sviluppatore deve garantire che l'app sia un listener efficace e stia ricevendo le specifiche della conversazione che soddisfano le aspettative dell'utente. Tuttavia, è anche possibile che potrebbero non avere fornito tutte le informazioni richieste dall'app per ottenere l'attività richiesta.

Questa situazione può essere gestita dall'app in diversi modi:

- **Scegliere un valore predefinito valido per il valore mancante** . ad esempio, un'app ride sharing potrebbe impostare come predefinita la posizione corrente dell'utente, se non è stato specificato il punto in cui volevano essere prelevati.
- **Eseguire un'ipotesi plausibile** : usando informazioni specifiche che l'app ha raccolto sull'utente, l'app potrebbe essere in grado di indovinare le informazioni mancanti, ad esempio inserendo un numero di cellulare mancante dalle informazioni di contatto dell'utente. È tuttavia necessario prestare attenzione per evitare spiacevoli sorprese, ad esempio per scegliere l'opzione più costosa e così via.
- **Richiedi altre informazioni** : l'app può avere una richiesta di conferma all'utente per il valore mancante. Tuttavia, la chiave in questo caso mantiene le conversazioni semplici e fino al punto. Gli utenti diventeranno rapidamente frustrati se devono rispondere a diverse domande per realizzare la richiesta.
- **Gestire le informazioni** in modo normale: l'utente potrebbe fornire un valore che l'app non era prevista o che non è in grado di gestire nel contesto specificato. Assicurarsi che l'app riporti questa situazione all'utente in modo da renderla chiara e facile da correggere.

Quando l'app viene visualizzata con un solo valore in questione, il modo migliore per gestire questa operazione è fare in modo che Siri chieda conferma all'utente. Ad esempio, *"si intendeva Bobo The Great?"* , a cui è possibile rispondere con una semplice risposta affermativa o no.

Quando si verifica una situazione in cui più scelte possibili potrebbero essere corrette per un singolo valore, la risoluzione dell'ambiguità è il metodo di gestione preferito. In questa situazione Siri può richiedere all'utente fino a dieci possibili opzioni tra cui scegliere. Ad esempio:

```csharp
Who do you want to send the message to?

* Bobo the Great!
* Bobo Jr.
* Little Bobo
```

Se ancora in questione, fare in modo che Siri chieda all'utente di fornire una risposta completamente nuova e più specifica per un determinato valore.

#### <a name="request-final-confirmation"></a>Conferma finale richiesta

Prima che l'app esegua effettivamente l'attività per soddisfare la richiesta dell'utente, Siri verificherà con l'estensione dell'app per assicurarsi che tutti gli elementi siano soddisfatti. Ad esempio, l'utente ha un importo sufficiente per l'account per effettuare il pagamento richiesto?

Inoltre, l'app deve garantire che fornisca tutte le informazioni possibili a Siri, in modo che possa presentarle all'utente e confermare che l'attività da eseguire soddisfi le proprie aspettative.

Dopo che l'utente ha confermato la richiesta e l'app l'ha eseguita, l'app deve verificare di nuovo che tutti i risultati siano stati restituiti a Siri, in modo da poterli collegare all'utente.

#### <a name="responding-to-the-request"></a>Risposta alla richiesta

Siri include diverse interfacce utente predefinite per ognuno dei domini e delle azioni che conosce. Tuttavia, laddove appropriato, l'app può fornire un'estensione personalizzata per l'interfaccia utente per migliorare l'esperienza utente presentando la personalizzazione dell'app e l'interfaccia utente o altre informazioni rispetto a quelle presenti nella richiesta.

Ciò premesso, è consigliabile usare il vincolo quando si progettano interfacce personalizzate per Siri. In genere, l'utente desidera eseguire un'attività specifica nel minor tempo possibile e non deve essere sottoposto a overload con informazioni non necessarie.

È necessario prestare attenzione anche per assicurarsi che l'interfaccia utente personalizzata appaia e risponda correttamente in tutti i diversi dispositivi iOS e gli orientamenti che l'utente potrebbe avere o usare il dispositivo.

Quando appropriato, usare l'API SiriKit per nascondere le informazioni ridondanti già presenti nell'interfaccia utente Siri predefinita. Assicurarsi tuttavia che l'app fornisca ancora le informazioni a Siri, in modo che possa presentarle in modo verbale in situazioni libere.

Potrebbero esserci situazioni in cui Siri avvierà l'app per soddisfare la richiesta dell'utente, ad esempio la presentazione delle foto richieste dall'utente. In questi casi, non sorprendere l'utente. Visualizzare le informazioni previste senza che siano necessari passaggi intermedi o un'ulteriore interazione. Non visualizzare mai le informazioni o eseguire un'attività non prevista dall'utente.

### <a name="polishing-the-design"></a>Lucidatura della progettazione

Sono disponibili diversi passaggi che Apple suggerisce di lucidare per la progettazione delle interfacce di conversazione. In primo luogo, consiste nel fornire esempi di vocabolario chiaro, conciso e casi d'uso a Siri.

Uno dei modi in cui un utente individua l'app è avviando una conversazione con Siri e chiedendo *"cosa è possibile fare?"* Siri mostrerà diverse operazioni, tra cui l'app dello sviluppatore e i casi d'uso del Hero di esempio forniti tramite il relativo `plist` file.

Come scrivere casi d'uso di esempio validi:

- Assicurarsi che gli esempi includano il nome dell'app.
- Lasciare l'esempio short e to-Point.
- Fornire più esempi per ognuno degli Intent supportati dall'app.
- Definire la priorità di entrambi gli Intent e gli esempi in essi basati sui casi d'uso più comuni per l'app.
- Assicurarsi che l'app fornisca esempi localizzati.
- Verificare che ogni esempio specificato funzioni come previsto nell'app.
- Evitare di indirizzare Siri negli esempi, quindi non includere testo come *"Hey Siri..."*
- Evitare i convenevoli superflui, ad esempio *"prego"* o *"Grazie*".

Prendere il momento opportuno per esplorare e sperimentare il modo in cui l'app può modellare la conversazione che Siri sta usando per conto dell'utente. Assicurarsi di comunicare con gli utenti tipici di tutto il processo, in quanto le interazioni con e le aspettative dell'app potrebbero cambiare nel corso del tempo.

Ricordarsi sempre di testare l'app in situazioni diverse e in tutti i diversi metodi per richiamare una conversazione con Siri. Testare in posizioni reali l'utente potrebbe usare l'app, lontano dall'ufficio e dal desk.

Sforzarsi di fare in modo che le conversazioni con Siri (per conto dell'app) siano fluide, naturali e "si sentono appena giuste".

## <a name="summary"></a>Riepilogo

Questo articolo ha illustrato i concetti chiave necessari per usare SiriKit e può interagire con le app Novell. iOS per fornire servizi accessibili all'utente tramite Siri e l'app Maps in un dispositivo iOS.




## <a name="related-links"></a>Collegamenti correlati

- [Esempio ElizaChat](https://docs.microsoft.com/samples/xamarin/ios-samples/ios10-elizachat)
- [Guida alla programmazione di SiriKit](https://developer.apple.com/library/prerelease/content/documentation/Intents/Conceptual/SiriIntegrationGuide/index.html)
- [Informazioni di riferimento sul Framework Intent](https://developer.apple.com/reference/intents)
- [Informazioni di riferimento sul Framework dell'interfaccia utente Intent](https://developer.apple.com/reference/intentsui)
