---
title: Aggiornamenti di SiriKit in iOS 11
description: Questo documento descrive come usare SiriKit in iOS 11. In particolare, viene esaminato come utilizzare attività e note e come specificare nomi alternativi per un'applicazione.
ms.prod: xamarin
ms.assetid: 8F75300B-B591-42ED-9D17-001992A5C381
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 09/07/2017
ms.openlocfilehash: ce4514059b2d0713cdf1e0a4a9956ab38aae7604
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73032142"
---
# <a name="sirikit-updates-in-ios-11"></a>Aggiornamenti di SiriKit in iOS 11

SiriKit è stato introdotto in iOS 10, con una serie di domini del servizio (compresi gli allenamenti, la prenotazione e l'esecuzione di chiamate). Vedere la [sezione SiriKit](~/ios/platform/sirikit/index.md) per i concetti relativi a SiriKit e come implementare SiriKit nell'app.

![Demo elenco attività Siri](sirikit-images/sirikit.png)

SiriKit in iOS 11 aggiunge questi nuovi e aggiornati domini Intent:

- [**Elenchi e note**](#listsnotes) -novità. Fornisce un'API per le app per l'elaborazione di attività e note.
- **Codici visivi** -novità. Siri può visualizzare codici QR per condividere le informazioni di contatto o partecipare alle transazioni di pagamento.
- **Pagamenti** : aggiunta di ricerche e trasferimenti per le interazioni di pagamento.
- **Corsa alla prenotazione** : aggiunta di Annulla corsa e suggerimenti per il feedback.

Le altre nuove funzionalità includono:

- [**Nomi alternativi**](#alternativenames) per le app: fornisce alias che consentono ai clienti di indicare a Siri di usare come destinazione l'app offrendo nomi o pronunce alternative.
- **Avvio degli allenamenti** : consente di avviare un allenamento in background.

Di seguito sono illustrate alcune di queste funzionalità. Per altri dettagli sugli altri, vedere la [documentazione di Apple SiriKit](https://developer.apple.com/documentation/sirikit).

<a name="listsnotes" />

## <a name="lists-and-notes"></a>Elenchi e note

Il nuovo dominio elenchi e note fornisce un'API per le app per elaborare le attività e le note tramite le richieste vocali Siri.

**Attività**

- Disporre di un titolo e di uno stato di completamento.
- Includere facoltativamente una scadenza e una località.

**Note**

- Avere un titolo e un campo di contenuto.

Sia le attività che le note possono essere organizzate in gruppi. Nella parte restante di questa sezione viene descritto come implementare questo nuovo dominio con SiriKit usando l' [esempio TasksNotes SiriKit](https://docs.microsoft.com/samples/xamarin/ios-samples/ios11-sirikitsample).

### <a name="how-to-process-a-sirikit-request"></a>Come elaborare una richiesta SiriKit

Elaborare una richiesta SiriKit attenendosi alla procedura seguente:

1. **Risolvere** : convalidare i parametri e richiedere ulteriori informazioni dall'utente (se necessario).
2. **Confermare** : convalida finale e verifica che la richiesta può essere elaborata.
3. **Handle** : eseguire l'operazione (aggiornamento dei dati o esecuzione di operazioni di rete).

I primi due passaggi sono facoltativi (anche se consigliati) ed è necessario il passaggio finale.
Sono disponibili istruzioni più dettagliate nella [sezione SiriKit](~/ios/platform/sirikit/index.md).

### <a name="resolve-and-confirm-methods"></a>Metodi di risoluzione e conferma

Questi metodi facoltativi consentono al codice di eseguire la convalida, selezionare le impostazioni predefinite o richiedere informazioni aggiuntive all'utente.

Per l'interfaccia `IINCreateTaskListIntent`, ad esempio, il metodo richiesto è `HandleCreateTaskList`. Sono disponibili quattro metodi facoltativi che forniscono un maggiore controllo sull'interazione Siri:

- `ResolveTitle`: convalida il titolo, imposta un titolo predefinito (se appropriato) o segnala che i dati non sono necessari.
- `ResolveTaskTitles`: convalida l'elenco delle attività pronunciate dall'utente.
- `ResolveGroupName`: convalida il nome del gruppo, sceglie un gruppo predefinito o segnala che i dati non sono necessari.
- `ConfirmCreateTaskList`: verifica che il codice sia in grado di eseguire l'operazione richiesta, ma non lo esegue (solo i metodi di `Handle*` devono modificare i dati).

### <a name="handle-the-intent"></a>Gestisci finalità

Nel dominio elenchi e note sono presenti sei Intent, tre per le attività e tre per le note.
I metodi che è necessario implementare per gestire questi Intent sono:

- Per le attività:
  - `HandleAddTasks`
  - `HandleCreateTaskList`
  - `HandleSetTaskAttribute`
- Per le note:
  - `HandleCreateNote`
  - `HandleAppendToNote`
  - `HandleSearchForNotebookItems`

A ogni metodo è stato passato un tipo di finalità specifico, che contiene tutte le informazioni di Siri analizzate dalla richiesta dell'utente (ed eventualmente aggiornate nei metodi `Resolve*` e `Confirm*`).
L'app deve analizzare i dati forniti, quindi eseguire alcune azioni per archiviare o in altro modo elaborare i dati e restituire un risultato che Siri parla e Mostra all'utente.

### <a name="response-codes"></a>Codici di risposta

I metodi `Handle*` e facoltativi `Confirm*` richiesti indicano un codice di risposta impostando un valore sull'oggetto che passano al gestore di completamento. Le risposte provengono dall'enumerazione `INCreateTaskListIntentResponseCode`:

- `Ready`: restituisce durante la fase di conferma (ad esempio, da un metodo di `Confirm*`, ma non da un metodo di `Handle*`).
- `InProgress`: utilizzato per le attività a esecuzione prolungata, ad esempio un'operazione di rete/server.
- `Success`: risponde con i dettagli dell'operazione riuscita (solo da un metodo di `Handle*`).
- `Failure`: indica che si è verificato un errore e non è stato possibile completare l'operazione.
- `RequiringAppLaunch`: non può essere elaborato dalla finalità, ma è possibile eseguire l'operazione nell'app.
- `Unspecified` – non utilizzare: il messaggio di errore verrà visualizzato all'utente.

Altre informazioni su questi metodi e risposte sono disponibili nella [documentazione relativa agli elenchi e alle note](https://developer.apple.com/documentation/sirikit/lists_and_notes)di Apple SiriKit.

### <a name="implementing-lists-and-notes"></a>Implementazione di elenchi e note

L' [esempio di SiriKit di TasksNotes](https://docs.microsoft.com/samples/xamarin/ios-samples/ios11-sirikitsample) è stato creato seguendo questa procedura per aggiungere il supporto di SiriKit a un'app iOS vuota.

Per prima cosa, per aggiungere il supporto SiriKit, seguire questa procedura per l'app iOS:

1. Casella di selezione **SiriKit** in **titles. plist**.
2. Aggiungere la chiave **privacy – Siri Usage Description** a **info. plist**, insieme a un messaggio per i clienti.
3. Chiamare il metodo `INPreferences.RequestSiriAuthorization` nell'app per richiedere all'utente di consentire le interazioni Siri.
4. Aggiungere SiriKit all'ID app nel portale per sviluppatori e creare nuovamente i profili di provisioning per includere il nuovo diritto.

Aggiungere quindi un nuovo progetto di estensione all'app per gestire le richieste Siri:

1. Fare clic con il pulsante destro del mouse sulla soluzione e scegliere **aggiungi > Aggiungi nuovo progetto...** .
2. Scegliere il modello di **estensione per iOS > estensione > Intent** .
3. Verranno aggiunti due nuovi progetti: Intent e IntentUI. La personalizzazione dell'interfaccia utente è facoltativa, pertanto nell'esempio è incluso solo il codice nel progetto **Intent** .

Il progetto di estensione è il punto in cui verranno elaborate tutte le richieste SiriKit. Come estensione separata, non dispone automaticamente di alcun modo per comunicare con l'app principale, che in genere viene risolta implementando l'archiviazione di file condivisi usando gruppi di app.

#### <a name="configure-the-intenthandler"></a>Configurare IntentHandler

La classe `IntentHandler` è il punto di ingresso per le richieste Siri. ogni finalità viene passata al metodo `GetHandler`, che restituisce un oggetto in grado di gestire la richiesta.

Il codice seguente illustra un'implementazione semplice:

```csharp
[Register("IntentHandler")]
public partial class IntentHandler : INExtension, IINNotebookDomainHandling
{
  protected IntentHandler(IntPtr handle) : base(handle)
  {}
  public override NSObject GetHandler(INIntent intent)
  {
    // This is the default implementation.  If you want different objects to handle different intents,
    // you can override this and return the handler you want for that particular intent.
    return this;
  }
  // add intent handlers here!
}
```

La classe deve ereditare da `INExtension`e, poiché l'esempio sta per gestire gli elenchi e gli intenti di note, implementa anche `IINNotebookDomainHandling`.

> [!NOTE]
>
> - In .NET esiste una convenzione che prevede che le interfacce siano precedute da un `I`di capitale, a cui Novell aderisce quando si associano protocolli da iOS SDK.
> - Novell conserva anche i nomi dei tipi da iOS e Apple usa i primi due caratteri nei nomi dei tipi per riflettere il Framework a cui appartiene un tipo.
> - Per il Framework di `Intents`, i tipi sono preceduti da `IN*`, ad esempio `INExtension`) ma queste _non_ sono interfacce.
> - Segue inoltre che i protocolli (che diventano interfacce in C#) finiscono con due`I`s, ad esempio`IINAddTasksIntentHandling`.

#### <a name="handling-intents"></a>Gestione degli Intent

Ogni finalità (Aggiungi attività, imposta attributo attività e così via) viene implementata in un singolo metodo simile a quello illustrato di seguito. Il metodo deve eseguire tre funzioni principali:

1. **Elaborare lo scopo** : i dati analizzati da Siri vengono resi disponibili in un oggetto `intent` specifico del tipo di Intent. È possibile che l'app abbia convalidato i dati usando metodi di `Resolve*` facoltativi.
2. **Convalidare e aggiornare l'archivio dati** : salvare i dati nel file System (usando gruppi di app in modo che l'app iOS principale possa accedervi) o tramite una richiesta di rete.
3. **Fornire la risposta** : usare il gestore `completion` per inviare una risposta a Siri per leggere/visualizzare l'utente:

```csharp
public void HandleCreateTaskList(INCreateTaskListIntent intent, Action<INCreateTaskListIntentResponse> completion)
{
  var list = TaskList.FromIntent(intent);
  // TODO: have to create the list and tasks... in your app data store
  var response = new INCreateTaskListIntentResponse(INCreateTaskListIntentResponseCode.Success, null)
  {
    CreatedTaskList = list
  };
  completion(response);
}
```

Si noti che `null` viene passato come secondo parametro alla risposta, ovvero il parametro dell'attività utente e, quando non viene specificato, verrà usato un valore predefinito.
È possibile impostare un tipo di attività personalizzato purché l'app iOS la supporti tramite la chiave `NSUserActivityTypes` in **info. plist**. È quindi possibile gestire questo caso quando l'app viene aperta ed eseguire operazioni specifiche, ad esempio l'apertura di un controller di visualizzazione pertinente e il caricamento dei dati dall'operazione Siri.

Nell'esempio viene inoltre hardcoded il risultato della `Success`, ma in scenari reali è necessario aggiungere una segnalazione errori corretta.

### <a name="test-phrases"></a>Frasi di test

Le frasi di test seguenti dovrebbero funzionare nell'app di esempio:

- "Crea un elenco di alimentari con mele, banane e pere in TasksNotes"
- "Aggiungi attività WWDC in TasksNotes"
- "Aggiungere l'attività WWDC all'elenco di formazione in TasksNotes"
- "Mark attendi WWDC come completato in TasksNotes"
- "In TasksNotes mi ricordo di acquistare un iPhone quando torno a casa"
- "Mark Buy iPhone come completato in TasksNotes"
- "Ricordami di lasciare la Home page alle 08:00 in TasksNotes"

![Creare un nuovo esempio di elenco](sirikit-images/createtasklist-sml.png) ![Esempio di impostazione dell'attività come completo](sirikit-images/settaskattribute-sml.png)

> [!NOTE]
> Il simulatore iOS 11 supporta il test con Siri (a differenza delle versioni precedenti).
>
> Se si esegue il test su dispositivi reali, non dimenticare di configurare l'ID app e i profili di provisioning per il supporto di SiriKit.

<a name="alternativenames" />

## <a name="alternative-names"></a>Nomi alternativi

Questa nuova funzionalità di iOS 11 significa che è possibile configurare nomi alternativi per l'app per consentire agli utenti di attivarli correttamente con Siri. Aggiungere le chiavi seguenti al file **info. plist** del progetto di app iOS:

![INFO. plist che Mostra chiavi e valori alternativi per il nome dell'app](sirikit-images/alternative-names.png)

Con i nomi di app alternativi impostati, le frasi seguenti funzioneranno anche per l'app di esempio (che è effettivamente denominata **TasksNotes**):

- "Crea un elenco di alimentari con mele, banane e pere in _MonkeyNotes_"
- "Aggiungi attività WWDC in _MonkeyTodo_"

## <a name="troubleshooting"></a>Troubleshooting

Alcuni errori che potrebbero verificarsi durante l'esecuzione dell'esempio o l'aggiunta di SiriKit alle applicazioni:

### <a name="nsinternalinconsistencyexception"></a>NSInternalInconsistencyException

_Eccezione Objective-C generata.  Nome: NSInternalInconsistencyException motivo: uso della classe < le preferenze: 0x60400082ff00 > da un'app richiede il diritto com. Apple. Developer. Siri. È stata abilitata la funzionalità Siri nel progetto Xcode?_

- SiriKit viene selezionato in **titles. plist**.
- Il file **titles. plist** viene configurato nelle **opzioni del progetto > compilare > la firma del bundle iOS**.

  [opzioni di![progetto che mostrano i diritti impostati correttamente](sirikit-images/set-entitlements-sml.png)](sirikit-images/set-entitlements.png#lightbox)

- (per la distribuzione dei dispositivi) Per l'ID app sono abilitati SiriKit e il profilo di provisioning.

## <a name="related-links"></a>Collegamenti correlati

- [SiriKit (Apple)](https://developer.apple.com/documentation/sirikit)
- [Esempio TasksNotes SiriKit](https://docs.microsoft.com/samples/xamarin/ios-samples/ios11-sirikitsample)
- [Novità di SiriKit (WWDC) (video)](https://developer.apple.com/videos/play/wwdc2017/214/)
