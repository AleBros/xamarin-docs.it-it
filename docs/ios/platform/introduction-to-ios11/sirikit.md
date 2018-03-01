---
title: SiriKit
ms.topic: article
ms.prod: xamarin
ms.assetid: 8F75300B-B591-42ED-9D17-001992A5C381
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 09/07/2017
ms.openlocfilehash: 0240dd5e381694a31ba9ebb12dd166ca0ef54750
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2018
---
# <a name="sirikit"></a>SiriKit

SiriKit è stata introdotta in iOS 10, con un numero di domini del servizio (incluso allenamenti, marcia prenotazione e chiamate). Consultare la [SiriKit sezione](~/ios/platform/sirikit/index.md) SiriKit concetti e sulle modalità di implementazione SiriKit nell'app.

![Demo di elenco attività Siri](sirikit-images/sirikit.png)

SiriKit in iOS 11 aggiunge questi domini preventivi nuovi e aggiornati:

- [**Elenca e note** ](#listsnotes) : New! Fornisce un'API per App per elaborare le attività e note.
- **I codici Visual** : New! Siri è possibile visualizzare i codici a matrice per condividere le informazioni di contatto o partecipare alle transazioni di pagamento.
- **Pagamenti** : aggiunta di tipi di trasferimento e ricerca per le interazioni di pagamento.
- **Sostituire prenotazione** : aggiunto Annulla intenti marcia e commenti e suggerimenti.

Altre nuove funzionalità includono:

- [**I nomi alternativi app** ](#alternativenames) – fornisce alias che consentono ai clienti informazioni Siri per app di destinazione tramite l'offerta di nomi alternativa/pronuncia.
- **Avvio allenamenti** – offre la possibilità di avviare accinge in background.

Alcune di queste funzionalità vengono spiegate di seguito. Per ulteriori informazioni sulle altre, fare riferimento a [documentazione di Apple SiriKit](https://developer.apple.com/documentation/sirikit).

<a name="listsnotes" />

## <a name="lists-and-notes"></a>Elenchi e note

Il nuovo dominio di elenchi e le note fornisce un'API per App per elaborare le attività e note tramite richieste vocale Siri.

**Attività**

- Disporre di un titolo e lo stato di completamento.
- Includere facoltativamente una scadenza e un percorso.

**Note**

- Disporre di un titolo e un campo contenuto.

Sia le attività e note possono essere organizzate in gruppi. Il resto di questa sezione viene descritto come implementare questo nuovo dominio con SiriKit, utilizzando il [TasksNotes SiriKit esempio](https://developer.xamarin.com/samples/monotouch/ios11/SiriKitSample/).

### <a name="how-to-process-a-sirikit-request"></a>Come elaborare una richiesta di SiriKit

Elaborare una richiesta SiriKit attenendosi alla procedura seguente:

1. **Risolvere** : convalidare i parametri e richiedere ulteriori informazioni da parte dell'utente (se richiesto).
2. **Conferma** : convalida finale e verifica che la richiesta può essere elaborata.
3. **Gestire** : eseguire l'operazione (aggiornamento di dati o l'esecuzione di operazioni di rete).

I primi due passaggi sono facoltativi (sebbene sconsigliato), e il passaggio finale è obbligatorio.
Sono disponibili istruzioni dettagliate nel [SiriKit sezione](~/ios/platform/sirikit/index.md).

### <a name="resolve-and-confirm-methods"></a>Risolvere e confermare di metodi

Questi metodi facoltativi consentono il codice di eseguire la convalida, selezionare le impostazioni predefinite o richiedere informazioni aggiuntive da parte dell'utente.

Ad esempio, per il `IINCreateTaskListIntent` interfaccia, il metodo richiesto è `HandleCreateTaskList`. Esistono quattro metodi facoltativi che garantiscono maggiore controllo sull'interazione di Siri:

- `ResolveTitle` -Convalida il titolo, imposta un titolo predefinito (se appropriato) o indica che i dati non sono obbligatori.
- `ResolveTaskTitles` : Consente di verificare l'elenco di attività leggere dall'utente.
- `ResolveGroupName` : Il nome del gruppo di convalida, sceglie un gruppo predefinito o segnala che i dati non sono necessari.
- `ConfirmCreateTaskList` : Verifica che il codice può eseguire l'operazione richiesta, ma non lo esegue (solo il `Handle*` metodi devono modificare i dati).

### <a name="handle-the-intent"></a>Lo scopo di gestire

Sono disponibili sei tipi nel dominio elenchi e le note, tre per le attività e tre per le note.
I metodi che è necessario implementare per gestire questi tipi sono:

- Per le attività:
  - `HandleAddTasks`
  - `HandleCreateTaskList`
  - `HandleSetTaskAttribute`
- Per le note:
  - `HandleCreateNote`
  - `HandleAppendToNote`
  - `HandleSearchForNotebookItems`

Ogni metodo presenta un tipo di intento specifico passato al metodo, che contiene tutte le informazioni Siri ha analizzato dalla richiesta dell'utente (e probabilmente aggiornato nel `Resolve*` e `Confirm*` metodi).
L'app deve analizzare i dati forniti, quindi eseguire alcune azioni per l'archiviazione o un processo in caso contrario i dati e restituire un risultato Siri legge e visualizza all'utente.

### <a name="response-codes"></a>Codici di risposta

Obbligatorio `Handle*` e facoltativi `Confirm*` indicano un codice di risposta impostando un valore per l'oggetto passato al gestore di completamento. Le risposte provengono dal `INCreateTaskListIntentResponseCode` enumerazione:

- `Ready` : Restituisce durante la fase di conferma (ie. da un `Confirm*` (metodo), ma non da un `Handle*` (metodo)).
- `InProgress` -Viene usato per l'attività a esecuzione prolungata (ad esempio un'operazione di rete o server).
- `Success` – Risponde con i dettagli dell'operazione ha esito positivo (solo da un `Handle*` (metodo)).
- `Failure` : Indica che si è verificato un errore e non è stato possibile completare l'operazione.
- `RequiringAppLaunch` : Non può elaborare la finalità, ma l'operazione è possibile nell'app.
- `Unspecified` : Non utilizzare: verrà visualizzato il messaggio di errore all'utente.

Ulteriori informazioni su questi metodi e le risposte in Apple [SiriKit Elenca e note sulla documentazione](https://developer.apple.com/documentation/sirikit/lists_and_notes).

### <a name="implementing-lists-and-notes"></a>Implementazione di elenchi e note

Il [TasksNotes SiriKit esempio](https://developer.xamarin.com/samples/monotouch/ios11/SiriKitSample/) è stato creato utilizzando la procedura seguente per aggiungere supporto SiriKit a un'app iOS vuota.

In primo luogo, per aggiungere il supporto di SiriKit, seguire questi passaggi per l'app iOS:

1. Segni di graduazione **SiriKit** in **Entitlements.plist**.
2. Aggiungere il **sulla Privacy: descrizione dell'utilizzo di Siri** per **Info. plist**, con un messaggio per i clienti.
3. Chiamare il `INPreferences.RequestSiriAuthorization` metodo nell'app, per richiedere all'utente per consentire le interazioni di Siri.
4. Aggiungere SiriKit all'ID di App del portale per sviluppatori e ricreare i profili di provisioning per includere i diritti di nuovo.

Aggiungere quindi un nuovo progetto di estensione per l'app per gestire le richieste di Siri:

1. Pulsante destro del mouse sulla soluzione e scegliere **Aggiungi > Aggiungi nuovo progetto...** .
2. Scegliere il **iOS > estensione > tipi di estensione** modello.
3. Verranno aggiunti due nuovi progetti: finalità e IntentUI. Personalizzazione dell'interfaccia utente è facoltativa, quindi l'esempio include solo il codice nel **finalità** progetto.

Il progetto di estensione è in tutte le richieste di SiriKit verranno elaborate. Come un'estensione diversa, automaticamente è un modo per comunicare con l'app principale – questo è in genere risolto mediante l'implementazione di archiviazione di file condiviso tramite gruppi di app.

#### <a name="configure-the-intenthandler"></a>Configurare il IntentHandler

Il `IntentHandler` classe è il punto di ingresso per le richieste di Siri: ogni finalità viene passato al `GetHandler` metodo, che restituisce un oggetto che può gestire la richiesta.

Il codice seguente viene illustrata un'implementazione semplice:

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

La classe deve ereditare da `INExtension`, e poiché il codice di esempio sta per la gestione di elenchi e note intenti, implementa inoltre `IINNotebookDomainHandling`.

> [!NOTE]
> **Nota sulla denominazione:** è una convenzione in .NET per le interfacce per essere preceduto dalla lettera maiuscola `I`, che rispetti Xamarin quando si associano i protocolli da iOS SDK.
>
> Xamarin mantiene anche i nomi dei tipi da iOS e Apple Usa i primi due caratteri nei nomi di tipo in modo da riflettere il framework di un tipo a cui appartiene.
>
> Per il `Intents` framework, i tipi sono precedute dal prefisso `IN*` (ad es. `INExtension`), ma si tratta di _non_ interfacce.
> Segue anche i protocolli (che diventano le interfacce in c#) finire con due `I`s, ad esempio `IINAddTasksIntentHandling`.

#### <a name="handling-intents"></a>La gestione dei tipi

Ogni finalità (Aggiungi attività, impostare attributi di attività e così via) viene implementata in un singolo metodo simile a quello riportato di seguito. Il metodo deve eseguire le tre funzioni principali:

1. **Lo scopo di elaborare** : I dati analizzati dalla Siri vengono resi disponibili in un `intent` specifico per il tipo delle finalità di oggetto. L'app può avere convalidato i dati di utilizzo facoltativo `Resolve*` metodi.
2. **Convalidare e aggiornare l'archivio dati** : salvare dati nel file System (tramite gruppi di App in modo che l'app iOS principale anche possibile accedervi) o tramite una richiesta di rete.
3. **Fornire una risposta** : utilizzo di `completion` gestore invia una risposta a Siri di lettura/visualizzare all'utente:

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

Si noti che `null` viene passato come secondo parametro per la risposta: questo è il parametro dell'attività utente e quando non viene fornito, verrà utilizzato un valore predefinito.
È possibile impostare un tipo di attività personalizzata, purché supportati dall'app iOS tramite il `NSUserActivityTypes` chiave in **Info. plist**. Si può gestire questa situazione, quando l'app viene aperto ed eseguire operazioni specifiche (ad esempio l'apertura di un controller di visualizzazione pertinenti e il caricamento dei dati dall'operazione di Siri).

Nell'esempio viene inoltre imposta come hardcoded il `Success` risultato, ma in scenari reali, devono essere aggiunti segnalazione di errore appropriato.

### <a name="test-phrases"></a>Frasi di test

Le frasi di test seguente dovrebbero funzionare nell'applicazione di esempio:

- "Verificare un elenco con mele banane e pere in TasksNotes"
- "Aggiungi attività WWDC in TasksNotes"
- "Aggiungi attività WWDC all'elenco di training in TasksNotes"
- "Contrassegno partecipare WWDC come completate in TasksNotes"
- "In TasksNotes promemoria per acquistare un iphone quando tornano a casa"
- "Contrassegno acquistare iPhone come completato nel TasksNotes"
- "Un promemoria per lasciare home in TasksNotes alle ore 8"

![Creare un nuovo esempio di elenco](sirikit-images/createtasklist-sml.png) ![Set di attività come esempio completo](sirikit-images/settaskattribute-sml.png)

> [!NOTE]
> IOS 11 simulatore supporta il test con Siri (a differenza delle versioni precedenti).
>
> Se i test sui dispositivi reali, non dimenticare di configurare l'ID di App e i profili di provisioning per SiriKit supportano.

<a name="alternativenames" />

## <a name="alternative-names"></a>Nomi alternativi

Questa nuova funzionalità di iOS 11 significa che è possibile configurare i nomi alternativi per l'app consentire agli utenti di attivare in modo corretto con Siri. Aggiungere le seguenti chiavi per il **Info. plist** file di progetto di app iOS:

![Info. plist che mostra i valori e chiavi con nome alternativo di app](sirikit-images/alternative-names.png)

Con il set di nomi di app alternativi, frasi seguenti funziona anche per l'app di esempio (effettivamente denominato **TasksNotes**):

- "Rendere un elenco con mele banane e pere _MonkeyNotes_"
- "Aggiungi attività WWDC in _MonkeyTodo_"


## <a name="troubleshooting"></a>Risoluzione dei problemi

Alcuni errori che potrebbero verificarsi durante l'esecuzione dell'esempio o per aggiungere le proprie applicazioni SiriKit:

### <a name="nsinternalinconsistencyexception"></a>NSInternalInconsistencyException

_Generata eccezione di Objective-C.  Nome: NSInternalInconsistencyException motivo: utilizzo della classe < INPreferences: 0x60400082ff00 > da un'app richiede il diritto com.apple.developer.siri. È stato abilitato la funzionalità di Siri nel progetto Xcode?_

- SiriKit sia selezionata **Entitlements.plist**.
- **Entitlements.plist** è configurato nel **opzioni progetto > compilare > firma Bundle iOS**.

  [![Opzioni progetto impostare correttamente i diritti di visualizzazione](sirikit-images/set-entitlements-sml.png)](sirikit-images/set-entitlements.png)

- (per una distribuzione dispositivo) ID dell'App ha SiriKit abilitato e profilo di provisioning scaricati.


## <a name="related-links"></a>Collegamenti correlati

- [SiriKit (mela)](https://developer.apple.com/documentation/sirikit)
- [Esempio SiriKit TasksNotes](https://developer.xamarin.com/samples/monotouch/ios11/SiriKitSample/)
- [Novità in SiriKit (WWDC) (video)](https://developer.apple.com/videos/play/wwdc2017/214/)
