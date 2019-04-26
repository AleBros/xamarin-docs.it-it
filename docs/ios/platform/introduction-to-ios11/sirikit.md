---
title: Aggiornamenti di SiriKit in iOS 11
description: Questo documento viene descritto come utilizzare SiriKit in iOS 11. In particolare, viene esaminato come lavorare con le attività e note e come fornire nomi alternativi per un'applicazione.
ms.prod: xamarin
ms.assetid: 8F75300B-B591-42ED-9D17-001992A5C381
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 09/07/2017
ms.openlocfilehash: 7e895dc2865880ec2789a40f8cdf047a20f8693b
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61400302"
---
# <a name="sirikit-updates-in-ios-11"></a>Aggiornamenti di SiriKit in iOS 11

SiriKit è stato introdotto in iOS 10, con un numero di domini del servizio (tra cui allenamenti, rani di prenotazione e chiamate). Vedere il [SiriKit sezione](~/ios/platform/sirikit/index.md) per i concetti di SiriKit e sull'implementazione di SiriKit nell'app.

![Demo di elenco attività Siri](sirikit-images/sirikit.png)

SiriKit in iOS 11 aggiunge questi domini preventivi nuovi e aggiornati:

- [**Elenca e note** ](#listsnotes) : nuovo! Fornisce un'API per le App per l'elaborazione di attività e note.
- **I codici Visual** : nuovo! Siri può visualizzare i codici a matrice per condividere le informazioni di contatto o partecipare a transazioni di pagamento.
- **I pagamenti** – aggiunta di ricerca e il trasferimento Intent per le interazioni di pagamento.
- **Recarsi prenotazione** – aggiunta annullare gli Intent rani e commenti e suggerimenti.

Le altre nuove funzionalità includono:

- [**I nomi delle app alternativi** ](#alternativenames) – fornisce gli alias che aiutano i clienti indicano a Siri di destinazione dell'app, offrendo i nomi alternativa/la pronuncia.
- **Avvio allenamenti** – offre la possibilità di avviare accinge in background.

Alcune di queste funzionalità vengono spiegati di seguito. Per altre informazioni sugli altri, consultare [documentazione di Apple SiriKit](https://developer.apple.com/documentation/sirikit).

<a name="listsnotes" />

## <a name="lists-and-notes"></a>Gli elenchi e le note

Il nuovo dominio di elenchi e le note fornisce un'API per le App per l'elaborazione di attività e note tramite richieste vocali Siri.

**Attività**

- Disporre di un titolo e lo stato di completamento.
- Facoltativamente, includere una scadenza e un percorso.

**Note**

- Disporre di un titolo e un campo del contenuto.

Entrambe le attività e note possono essere organizzate in gruppi. La parte restante di questa sezione viene descritto come implementare questo nuovo dominio con SiriKit, usando il [TasksNotes SiriKit esempio](https://developer.xamarin.com/samples/monotouch/ios11/SiriKitSample/).

### <a name="how-to-process-a-sirikit-request"></a>Come elaborare una richiesta di SiriKit

Elaborare una richiesta di SiriKit seguendo questa procedura:

1. **Risolvere** : convalidare i parametri e richiedere ulteriori informazioni da parte dell'utente (se richiesto).
2. **Conferma** – convalida finale e verifica che la richiesta può essere elaborata.
3. **Gestire** : eseguire l'operazione (l'aggiornamento dei dati o l'esecuzione di operazioni di rete).

I primi due passaggi sono facoltativi (sebbene invitati), e l'ultimo passaggio è obbligatorio.
Sono disponibili istruzioni più dettagliate nel [SiriKit sezione](~/ios/platform/sirikit/index.md).

### <a name="resolve-and-confirm-methods"></a>Risolvere e confermare i metodi

Questi metodi facoltativi consentono al codice di eseguire la convalida, selezionare le impostazioni predefinite o richiedere ulteriori informazioni da parte dell'utente.

Ad esempio, per il `IINCreateTaskListIntent` interfaccia, il metodo richiesto è `HandleCreateTaskList`. Esistono quattro metodi facoltativi che garantiscono maggiore controllo sull'interazione di Siri:

- `ResolveTitle` : Convalida del titolo, imposta il titolo predefinito (se appropriato) o segnala che i dati non sono obbligatori.
- `ResolveTaskTitles` – Consente di verificare l'elenco di attività letto dall'utente.
- `ResolveGroupName` : Convalida il nome del gruppo, sceglie un gruppo predefinito o segnala che i dati non sono obbligatori.
- `ConfirmCreateTaskList` : Convalida che il codice può eseguire l'operazione richiesta, ma non esegue (solo il `Handle*` metodi devono modificare i dati).

### <a name="handle-the-intent"></a>Gestire la finalità

Sono disponibili sei Intent nel dominio elenchi e le note, tre per le attività e tre le note.
I metodi che è necessario implementare per gestire questi Intent sono:

- Per le attività:
  - `HandleAddTasks`
  - `HandleCreateTaskList`
  - `HandleSetTaskAttribute`
- Per le note:
  - `HandleCreateNote`
  - `HandleAppendToNote`
  - `HandleSearchForNotebookItems`

Ogni metodo presenta un tipo di intent specifico passato al metodo, che contiene tutte le informazioni di Siri ha analizzato dalla richiesta dell'utente (e possibilmente aggiornati il `Resolve*` e `Confirm*` metodi).
L'app deve analizzare i dati forniti, quindi eseguire alcune azioni per l'archiviazione o in caso contrario elaborare i dati e restituire un risultato Siri legge e visualizza all'utente.

### <a name="response-codes"></a>Codici di risposta

Richiesti `Handle*` facoltative e `Confirm*` metodi indicano un codice di risposta impostando un valore per l'oggetto passato al gestore di completamento. Le risposte provengono dal `INCreateTaskListIntentResponseCode` enumerazione:

- `Ready` – Restituisce durante la fase di conferma (ie. da un `Confirm*` metodo, ma non da un `Handle*` (metodo)).
- `InProgress` : Usato per attività a esecuzione prolungata (ad esempio, un'operazione di rete o server).
- `Success` – Risponde con i dettagli dell'operazione ha esito positivo (solo da un `Handle*` (metodo)).
- `Failure` – Significa che si è verificato un errore e non è stato possibile completare l'operazione.
- `RequiringAppLaunch` -Non può elaborare l'intento, ma l'operazione può essere eseguita nell'app.
- `Unspecified` – Non utilizzare: messaggio di errore verrà visualizzato all'utente.

Altre informazioni su questi metodi e le risposte di Apple [SiriKit Elenca e documentazione delle note](https://developer.apple.com/documentation/sirikit/lists_and_notes).

### <a name="implementing-lists-and-notes"></a>Implementazione di elenchi e note

Il [TasksNotes SiriKit esempio](https://developer.xamarin.com/samples/monotouch/ios11/SiriKitSample/) è stata creata usando la procedura seguente per aggiungere il supporto di SiriKit a un'app iOS vuota.

In primo luogo, per aggiungere il supporto di SiriKit, seguire questi passaggi per l'app iOS:

1. Segni di graduazione **SiriKit** nelle **entitlements. plist**.
2. Aggiungere il **Privacy-descrizione utilizzo Siri** chiave **Info. plist**, insieme a un messaggio per i clienti.
3. Chiamare il `INPreferences.RequestSiriAuthorization` metodo nell'app, per richiedere all'utente di consentire interazioni Siri.
4. Aggiungere SiriKit all'ID App nel portale per sviluppatori e ricreare i profili di provisioning per includere il nuovo entitlement.

Aggiungere quindi un nuovo progetto di estensione per l'app per gestire le richieste Siri:

1. Pulsante destro del mouse sulla soluzione e scegliere **Aggiungi > Aggiungi nuovo progetto...** .
2. Scegliere il **iOS > estensioni > estensione per Intent** modello.
3. Verranno aggiunti due nuovi progetti: Finalità e IntentUI. Personalizzazione dell'interfaccia utente è facoltativa, quindi l'esempio include solo il codice nel **finalità** progetto.

Il progetto di estensione è in cui verranno elaborate tutte le richieste di SiriKit. Come un'estensione separata, dispone automaticamente alcun modo per comunicare con l'app principale, questo viene in genere risolto mediante l'implementazione di archiviazione file condiviso tramite i gruppi di app.

#### <a name="configure-the-intenthandler"></a>Configurare il IntentHandler

Il `IntentHandler` classe è il punto di ingresso per le richieste Siri: passato a ogni scopo il `GetHandler` metodo, che restituisce un oggetto che può gestire la richiesta.

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

La classe deve ereditare da `INExtension`, e poiché il codice di esempio sta per la gestione degli elenchi e Intent di notes, implementa inoltre `IINNotebookDomainHandling`.

> [!NOTE]
> - Vi è una convenzione in .NET per le interfacce per essere preceduto da una lettera maiuscola `I`, che rispetti Xamarin quando si associano i protocolli da iOS SDK.
> - Xamarin consente anche di mantenere i nomi dei tipi da iOS e Apple Usa i primi due caratteri nei nomi di tipo in modo da riflettere il framework di un tipo a cui appartiene.
> - Per il `Intents` framework, i tipi sono preceduti `IN*` (ad es. `INExtension`), ma si tratta _non_ interfacce.
> - Segue anche che i protocolli (che diventano le interfacce in C#) finire con due `I`s, ad esempio `IINAddTasksIntentHandling`.

#### <a name="handling-intents"></a>Gestisce gli Intent

Ogni finalità (Aggiungi attività, imposta l'attributo attività e così via) viene implementato in un singolo metodo simile a quello riportato di seguito. Il metodo deve eseguire tre funzioni principali:

1. **Lo scopo di elaborare** : I dati analizzati da Siri vengono resi disponibili in un `intent` oggetto specifica per il tipo di intent. L'app potrebbe aver convalidato tali dati facoltativa `Resolve*` metodi.
2. **Convalidare e aggiornare l'archivio dati** , salvare i dati nel file System (tramite i gruppi di App in modo che l'app iOS principale possa inoltre accedervi) o tramite una richiesta di rete.
3. **Fornire risposta** : usare il `completion` gestore invia una risposta a Siri di lettura/visualizzare all'utente:

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

Si noti che `null` viene passato come secondo parametro per la risposta – si tratta del parametro di attività utente e quando non viene specificato, verrà usato un valore predefinito.
È possibile impostare un tipo di attività personalizzata, purché l'app per iOS supporta quest'ultima con il `NSUserActivityTypes` chiavi in **Info. plist**. È quindi possibile gestire questa situazione, quando l'app viene aperta ed eseguire operazioni specifiche (ad esempio apertura di un controller di visualizzazione pertinenti e il caricamento dei dati dall'operazione di Siri).

L'esempio imposta anche il `Success` risultato, ma in scenari reali, segnalazione errori appropriate devono essere aggiunti.

### <a name="test-phrases"></a>Testare le frasi

Le frasi di test seguente dovrebbero funzionare nell'app di esempio:

- "Rendere una lista della spesa con mele bananas e pere in TasksNotes"
- "Aggiungi attività WWDC in TasksNotes"
- "Aggiungi attività WWDC all'elenco di corsi di formazione in TasksNotes"
- "Contrassegno partecipare WWDC come completate in TasksNotes"
- "In TasksNotes invia un promemoria per acquistare un iphone, quando recupera la principale"
- "Contrassegno buy iPhone completato nel TasksNotes"
- "Invia un promemoria a lasciare home alle 8:00 in TasksNotes"

![Creare un nuovo esempio di elenco](sirikit-images/createtasklist-sml.png) ![Set di attività come esempio completo](sirikit-images/settaskattribute-sml.png)

> [!NOTE]
> Simulatore di iOS 11 supporta test con Siri (a differenza delle versioni precedenti).
>
> Se i test su dispositivi reali, non dimenticare di configurare l'ID dell'App e i profili di provisioning per SiriKit supportano.

<a name="alternativenames" />

## <a name="alternative-names"></a>Nomi alternativi

Questa nuova funzionalità iOS 11 significa che è possibile configurare nomi alternativi per l'app consentire agli utenti di attivare correttamente con Siri. Aggiungere le seguenti chiavi per il **Info. plist** file di progetto dell'app iOS:

![File Info. plist che mostra i valori e le chiavi con nome alternativo di app](sirikit-images/alternative-names.png)

Con il set di nomi alternativi app, le frasi seguenti funziona anche per le app di esempio (che è in realtà denominato **TasksNotes**):

- "Rendere una lista della spesa con mele bananas e pere _MonkeyNotes_"
- "Aggiungi attività in WWDC _MonkeyTodo_"


## <a name="troubleshooting"></a>Risoluzione dei problemi

Alcuni errori che potrebbero verificarsi durante l'esecuzione del campione o l'aggiunta di SiriKit alle applicazioni:

### <a name="nsinternalinconsistencyexception"></a>NSInternalInconsistencyException

_Generata eccezione di Objective-C.  Nome: Motivo NSInternalInconsistencyException: Uso della classe < INPreferences: 0x60400082ff00 > da un'app richiede la com.apple.developer.siri entitlement. È stato abilitato la funzionalità di Siri nel progetto Xcode?_

- SiriKit sia selezionata nel **entitlements. plist**.
- **Entitlements. plist** configurato nel **opzioni progetto > compilazione > firma del Bundle iOS**.

  [![Opzioni del progetto che mostra che gli Entitlement impostate correttamente](sirikit-images/set-entitlements-sml.png)](sirikit-images/set-entitlements.png#lightbox)

- (per la distribuzione del dispositivo) ID dell'App ha SiriKit abilitata e scaricato il profilo di provisioning.


## <a name="related-links"></a>Collegamenti correlati

- [SiriKit (Apple)](https://developer.apple.com/documentation/sirikit)
- [Esempio di SiriKit TasksNotes](https://developer.xamarin.com/samples/monotouch/ios11/SiriKitSample/)
- [What ' s New in SiriKit (WWDC) (video)](https://developer.apple.com/videos/play/wwdc2017/214/)
