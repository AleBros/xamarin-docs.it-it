---
title: Ciclo di vita dell'attività
description: Le attività sono un blocco predefinito fondamentale di applicazioni Android e possono esistere in diversi Stati. Il ciclo di vita dell'attività inizia con la creazione di un'istanza e termina con la distruzione e include molti Stati. Quando un'attività modifica lo stato, viene chiamato il metodo di evento del ciclo di vita appropriato, che notifica l'attività della modifica dello stato imminente e consente di eseguire il codice per adattarsi a tale modifica. In questo articolo viene esaminato il ciclo di vita delle attività e viene illustrata la responsabilità di un'attività nel corso di ognuna di queste modifiche allo stato in modo che faccia parte di un'applicazione affidabile e ben comportata.
ms.prod: xamarin
ms.assetid: 05B34788-F2D2-4347-B66B-40AFD7B1D167
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/28/2018
ms.openlocfilehash: 8ebc52936dfdcb6b5262424eba5652de0b8908e0
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/06/2019
ms.locfileid: "70755602"
---
# <a name="activity-lifecycle"></a>Ciclo di vita dell'attività

_Le attività sono un blocco predefinito fondamentale di applicazioni Android e possono esistere in diversi Stati. Il ciclo di vita dell'attività inizia con la creazione di un'istanza e termina con la distruzione e include molti Stati. Quando un'attività modifica lo stato, viene chiamato il metodo di evento del ciclo di vita appropriato, che notifica l'attività della modifica dello stato imminente e consente di eseguire il codice per adattarsi a tale modifica. In questo articolo viene esaminato il ciclo di vita delle attività e viene illustrata la responsabilità di un'attività nel corso di ognuna di queste modifiche allo stato in modo che faccia parte di un'applicazione affidabile e ben comportata._

## <a name="activity-lifecycle-overview"></a>Panoramica del ciclo di vita delle attività

Le attività sono un concetto di programmazione insolito specifico per Android. Nello sviluppo di applicazioni tradizionali è in genere disponibile un metodo Main statico, che viene eseguito per avviare l'applicazione. Con Android, tuttavia, le cose sono diverse; Le applicazioni Android possono essere avviate tramite qualsiasi attività registrata all'interno di un'applicazione. In pratica, la maggior parte delle applicazioni avrà solo un'attività specifica specificata come punto di ingresso dell'applicazione. Tuttavia, se un'applicazione si arresta in modo anomalo o viene terminata dal sistema operativo, il sistema operativo può provare a riavviare l'applicazione in corrispondenza dell'ultima attività aperta o in qualsiasi altra posizione all'interno dello stack di attività precedente.
Inoltre, il sistema operativo può sospendere le attività quando non sono attive e recuperarle se la memoria è insufficiente. È necessario prendere in considerazione un'attenta considerazione per consentire all'applicazione di ripristinare correttamente lo stato nel caso in cui un'attività venga riavviata, specialmente se tale attività dipende dai dati delle attività precedenti.

Il ciclo di vita dell'attività viene implementato come una raccolta di metodi che il sistema operativo chiama per tutto il ciclo di vita di un'attività. Questi metodi consentono agli sviluppatori di implementare la funzionalità necessaria per soddisfare i requisiti di gestione dello stato e delle risorse delle applicazioni.

È estremamente importante per lo sviluppatore di applicazioni analizzare i requisiti di ogni attività per determinare quali metodi esposti dal ciclo di vita dell'attività devono essere implementati. In caso contrario, è possibile che si verifichino instabilità, arresti anomali, anomalie delle risorse e probabilmente persino un'instabilità del sistema operativo sottostante.

In questo capitolo viene esaminato in dettaglio il ciclo di vita delle attività, tra cui:

- Stati di attività
- Metodi del ciclo di vita
- Conservazione dello stato di un'applicazione

Questa sezione include anche una [procedura dettagliata](~/android/app-fundamentals/activity-lifecycle/saving-state.md) che fornisce esempi pratici su come salvare lo stato in modo efficiente durante il ciclo di vita dell'attività. Alla fine di questo capitolo è necessario conoscere il ciclo di vita delle attività e come supportarlo in un'applicazione Android.

## <a name="activity-lifecycle"></a>Ciclo di vita dell'attività

Il ciclo di vita dell'attività Android è costituito da una raccolta di metodi esposti all'interno della classe Activity che forniscono allo sviluppatore un Framework di gestione delle risorse. Questo Framework consente agli sviluppatori di soddisfare i requisiti univoci di gestione dello stato di ogni attività all'interno di un'applicazione e gestire correttamente la gestione delle risorse.

### <a name="activity-states"></a>Stati di attività

Il sistema operativo Android arbitraggio le attività in base al relativo stato. Ciò consente ad Android di identificare le attività che non sono più in uso, consentendo al sistema operativo di recuperare memoria e risorse. Il diagramma seguente illustra gli Stati che un'attività può attraversare durante la sua durata:

[![Diagramma degli Stati dell'attività](images/image1-sml.png)](images/image1.png#lightbox)

Questi Stati possono essere suddivisi in 4 gruppi principali, come indicato di seguito:

1. *Attivo o in esecuzione* &ndash; Le attività sono considerate attive o in esecuzione se sono in primo piano, anche note come parte superiore dello stack di attività. Questa operazione è considerata l'attività con priorità più elevata in Android e, di conseguenza, verrà terminata solo dal sistema operativo in situazioni estreme, ad esempio se l'attività tenta di usare una quantità di memoria superiore a quella disponibile nel dispositivo, perché ciò potrebbe causare la mancata risposta dell'interfaccia utente.

1. In *pausa* &ndash; Quando il dispositivo passa alla modalità di sospensione o un'attività rimane visibile ma parzialmente nascosta da una nuova attività non completa o trasparente, l'attività viene considerata sospesa. Le attività sospese sono ancora attive, ovvero mantengono tutte le informazioni sullo stato e sui membri e rimangono collegate a gestione finestre. Considerata come la seconda attività con priorità più elevata in Android e, di conseguenza, verrà terminata dal sistema operativo solo se la chiusura di questa attività soddisferà i requisiti di risorse necessari per assicurare la stabilità e la reattività dell'attività attiva/in esecuzione.

1. *Arrestato/in background* &ndash; Le attività completamente nascoste da un'altra attività vengono considerate interrotte o in background.
    Le attività interrotte continuano a mantenere le informazioni sullo stato e sui membri per il periodo di tempo più lungo possibile, ma le attività interrotte sono considerate la priorità più bassa dei tre Stati e, di conseguenza, il sistema operativo eliminerà prima le attività in questo stato per soddisfare la risorsa requisiti delle attività con priorità più elevata.

1. *Riavviato* &ndash; È possibile che un'attività che si trova in una posizione qualsiasi da sospesa a arrestata nel ciclo di vita venga rimossa dalla memoria da Android. Se l'utente torna all'attività, deve essere riavviato, ripristinato nello stato salvato in precedenza e quindi visualizzato all'utente.

### <a name="activity-re-creation-in-response-to-configuration-changes"></a>Ricreazione dell'attività in risposta alle modifiche alla configurazione

Per rendere più complessa la questione, Android genera una maggiore chiave nella combinazione denominata modifiche di configurazione. Le modifiche di configurazione sono cicli rapidi di distruzione/ricreazione di attività che si verificano quando viene modificata la configurazione di un'attività, ad esempio quando il dispositivo viene [ruotato](~/android/app-fundamentals/handling-rotation.md) (e l'attività deve essere ricompilata in modalità orizzontale o verticale) quando il viene visualizzata la tastiera (e l'attività viene presentata con la possibilità di ridimensionarsi) o quando il dispositivo viene inserito in un ancoraggio, tra gli altri.

Le modifiche alla configurazione continuano a causare le stesse modifiche allo stato dell'attività che si verificano durante l'arresto e il riavvio di un'attività. Tuttavia, per assicurarsi che un'applicazione risponda e funzioni correttamente durante le modifiche alla configurazione, è importante che vengano gestite nel minor tempo possibile. Per questo motivo, Android dispone di un'API specifica che può essere usata per salvare lo stato durante le modifiche di configurazione.
Questa operazione verrà trattata in un secondo momento nello [stato di gestione nell'intera sezione del ciclo](~/android/app-fundamentals/activity-lifecycle/index.md#Managing_State_Throughout_the_Lifecycle) di vita.

### <a name="activity-lifecycle-methods"></a>Metodi del ciclo di vita delle attività

Il Android SDK e, per estensione, il Framework Novell. Android fornisce un modello potente per la gestione dello stato delle attività all'interno di un'applicazione. Quando lo stato di un'attività viene modificato, l'attività viene notificata dal sistema operativo, che chiama metodi specifici su tale attività. Il diagramma seguente illustra questi metodi in relazione al ciclo di vita dell'attività:

[![Diagramma di flusso ciclo di vita attività](images/image2-sml.png)](images/image2.png#lightbox)

Gli sviluppatori possono gestire le modifiche di stato eseguendo l'override di questi metodi all'interno di un'attività. È importante notare, tuttavia, che tutti i metodi del ciclo di vita vengono chiamati sul thread dell'interfaccia utente e impediscono al sistema operativo di eseguire la parte successiva del lavoro dell'interfaccia utente, ad esempio nascondere l'attività corrente, visualizzare una nuova attività e così via. Di conseguenza, il codice in questi metodi dovrebbe essere il più breve possibile per garantire l'esecuzione di un'applicazione. Tutte le attività a esecuzione prolungata devono essere eseguite in un thread in background.

Esaminiamo ognuno di questi metodi del ciclo di vita e il relativo uso:

#### <a name="oncreate"></a>OnCreate

[OnCreate](xref:Android.App.Activity.OnCreate*) è il primo metodo da chiamare quando viene creata un'attività.
`OnCreate`viene sempre sottoposto a override per eseguire le inizializzazioni di avvio che possono essere richieste da un'attività, ad esempio:

- Creazione di visualizzazioni
- Inizializzazione di variabili
- Associazione di dati statici a elenchi

`OnCreate`accetta un parametro [bundle](xref:Android.OS.Bundle) , ovvero un dizionario per l'archiviazione e il passaggio di informazioni sullo stato e oggetti tra le attività se il bundle non è null, indica che l'attività viene riavviata e che deve ripristinare lo stato dell'istanza precedente. Il codice seguente illustra come recuperare i valori dal bundle:

```csharp
protected override void OnCreate(Bundle bundle)
{
   base.OnCreate(bundle);

   string intentString;
   bool intentBool;

   if (bundle != null)
   {
      intentString = bundle.GetString("myString");
      intentBool = bundle.GetBoolean("myBool");
   }

   // Set our view from the "main" layout resource
   SetContentView(Resource.Layout.Main);
}
```

Al termine, Android `OnStart`chiamerà. `OnCreate`

#### <a name="onstart"></a>OnStart

[OnStart](xref:Android.App.Activity.OnStart) viene sempre chiamato dal sistema dopo il completamento di `OnCreate`. Le attività possono eseguire l'override di questo metodo se devono eseguire attività specifiche immediatamente prima che un'attività diventi visibile, ad esempio l'aggiornamento dei valori correnti delle visualizzazioni all'interno dell'attività. Android chiamerà immediatamente dopo questo metodo. `OnResume`

#### <a name="onresume"></a>OnResume

Il sistema chiama [onResume](xref:Android.App.Activity.OnResume) quando l'attività è pronta per iniziare a interagire con l'utente.
Le attività devono eseguire l'override di questo metodo per eseguire attività quali:

- Aumento delle frequenze di fotogrammi (un'attività comune nello sviluppo di giochi)
- Avvio di animazioni
- Ascolto degli aggiornamenti GPS
- Visualizzare eventuali avvisi o finestre di dialogo rilevanti
- Collegare i gestori eventi esterni

Come esempio, il frammento di codice seguente mostra come inizializzare la fotocamera:

```csharp
public void OnResume()
{
    base.OnResume(); // Always call the superclass first.

    if (_camera==null)
    {
        // Do camera initializations here
    }
}
```

`OnResume`è importante perché tutte le operazioni eseguite in `OnPause` devono essere annullate in `OnResume`, poiché si tratta dell'unico metodo del ciclo di vita che è garantito per `OnPause` l'esecuzione dopo il ritorno all'attività.

#### <a name="onpause"></a>OnPause

[OnPause](xref:Android.App.Activity.OnPause) viene chiamato quando il sistema sta per inserire l'attività in background o quando l'attività viene nascosta parzialmente. Le attività devono eseguire l'override di questo metodo se è necessario:

- Eseguire il commit delle modifiche non salvate nei dati persistenti

- Eliminare o eliminare altri oggetti che utilizzano risorse

- Suddividere le frequenze fotogrammi e sospendere le animazioni

- Annulla la registrazione di gestori di eventi esterni o gestori di notifiche, ad esempio quelli collegati a un servizio. Questa operazione deve essere eseguita per evitare perdite di memoria di attività.

- Analogamente, se l'attività ha visualizzato qualsiasi finestra di dialogo o avviso, è necessario pulirla con `.Dismiss()` il metodo.

Ad esempio, il frammento di codice seguente rilascerà la fotocamera, perché non può essere usata dall'attività mentre è in pausa:

```csharp
public void OnPause()
{
    base.OnPause(); // Always call the superclass first

    // Release the camera as other activities might need it
    if (_camera != null)
    {
        _camera.Release();
        _camera = null;
    }
}
```

Esistono due possibili metodi del ciclo di vita che verranno chiamati `OnPause`dopo:

1. `OnResume`verrà chiamato se l'attività deve essere restituita in primo piano.
1. `OnStop`verrà chiamato se l'attività viene posizionata in background.

#### <a name="onstop"></a>OnStop

[OnStop](xref:Android.App.Activity.OnStop) viene chiamato quando l'attività non è più visibile all'utente. Ciò si verifica quando si verifica una delle condizioni seguenti:

- Viene avviata una nuova attività che copre questa attività.
- Un'attività esistente viene portata in primo piano.
- L'attività viene eliminata definitivamente.

`OnStop`potrebbe non essere sempre chiamato in situazioni di memoria insufficiente, ad esempio quando Android è affamato di risorse e non è in grado di eseguire correttamente il background dell'attività. Per questo motivo, è consigliabile non fare affidamento sulla `OnStop` chiamata quando si prepara un'attività per la distruzione. I successivi metodi del ciclo di vita che possono essere chiamati dopo questa `OnDestroy` operazione saranno se l'attività è in uscita `OnRestart` o se l'attività viene nuovamente intervenuta per interagire con l'utente.

#### <a name="ondestroy"></a>OnDestroy

[OnDestroy](xref:Android.App.Activity.OnDestroy) è il metodo finale chiamato su un'istanza dell'attività prima che venga distrutto e rimosso completamente dalla memoria. In situazioni estreme Android può terminare il processo dell'applicazione che ospita l'attività, che `OnDestroy` non verrà richiamata. La maggior parte delle attività non implementerà questo metodo perché la maggior parte della pulizia e dell' `OnPause` arresto è stata eseguita nei metodi e. `OnStop` Viene `OnDestroy` in genere eseguito l'override del metodo per pulire le risorse con esecuzione prolungata che potrebbero comportare perdite di risorse. Un esempio potrebbe essere costituito da thread in background avviati `OnCreate`in.

Non ci saranno metodi del ciclo di vita chiamati dopo che l'attività è stata eliminata.

#### <a name="onrestart"></a>OnRestart

[OnRestart](xref:Android.App.Activity.OnRestart) viene chiamato dopo l'arresto dell'attività, prima che venga riavviata. Un esempio valido è quando l'utente preme il pulsante Home mentre è in corso un'attività nell'applicazione. Quando si verifica `OnPause` questa situazione `OnStop` e vengono chiamati i metodi e l'attività viene spostata in background, ma non viene distrutta. Se l'utente dovesse ripristinare l'applicazione usando Gestione attività o un'applicazione simile, Android chiamerà il `OnRestart` metodo dell'attività.

Non sono disponibili linee guida generali per il tipo di logica che deve essere `OnRestart`implementata in. Questo perché `OnStart` viene sempre richiamato indipendentemente dal fatto che l'attività venga creata o riavviata, quindi le risorse richieste dall'attività devono essere inizializzate in `OnStart`, anziché `OnRestart`.

Il successivo metodo del ciclo di `OnRestart` vita chiamato `OnStart`dopo sarà.

### <a name="back-vs-home"></a>Indietro rispetto a Home

Molti dispositivi Android hanno due pulsanti distinti: un pulsante "indietro" e un pulsante "Home". Un esempio di questo problema si può osservare nella schermata seguente di Android 4.0.3:

[![Pulsanti indietro e Home](images/image4-sml.png)](images/image4.png#lightbox)

Esiste una lieve differenza tra i due pulsanti, anche se sembrano avere lo stesso effetto di inserire un'applicazione in background. Quando un utente fa clic sul pulsante indietro, indica a Android che viene eseguita con l'attività. Android eliminerà l'attività. Al contrario, quando l'utente fa clic sul pulsante Home, l'attività viene semplicemente posizionata &ndash; in background Android non eliminerà l'attività.

<a name="Managing_State_Throughout_the_Lifecycle" />

## <a name="managing-state-throughout-the-lifecycle"></a>Gestione dello stato durante tutto il ciclo di vita

Quando un'attività viene arrestata o distrutta, il sistema fornisce la possibilità di salvare lo stato dell'attività per una successiva riattivazione.
Questo stato salvato viene definito stato dell'istanza. Android offre tre opzioni per archiviare lo stato dell'istanza durante il ciclo di vita dell'attività:

1. Archiviazione di valori primitivi `Dictionary` in un noto come [bundle](xref:Android.OS.Bundle) che Android userà per salvare lo stato.

1. Creazione di una classe personalizzata che conterrà valori complessi, ad esempio bitmap. Android userà questa classe personalizzata per salvare lo stato.

1. Aggirare il ciclo di vita delle modifiche della configurazione e assumere la responsabilità completa della gestione dello stato nell'attività.

In questa guida vengono illustrate le prime due opzioni.

### <a name="bundle-state"></a>Stato bundle

L'opzione principale per salvare lo stato dell'istanza consiste nell'usare un oggetto dizionario chiave/valore noto come [bundle](xref:Android.OS.Bundle).
Tenere presente che, quando viene creata un'attività `OnCreate` a cui al metodo viene passato un bundle come parametro, è possibile usare questo bundle per ripristinare lo stato dell'istanza. Non è consigliabile usare un bundle per dati più complessi che non verranno serializzati in modo rapido o semplice in coppie chiave/valore (ad esempio, bitmap); ma deve essere usato per semplici valori come le stringhe.

Un'attività fornisce metodi che consentono di salvare e recuperare lo stato dell'istanza nel bundle:

- [OnSaveInstanceState](xref:Android.App.Activity.OnSaveInstanceState*) &ndash; Questa operazione viene richiamata da Android quando l'attività viene eliminata definitivamente. Le attività possono implementare questo metodo se devono rendere permanente gli elementi di stato chiave/valore.

- [OnRestoreInstanceState](xref:Android.App.Activity.OnRestoreInstanceState*) Questo metodo viene chiamato dopo `OnCreate` il completamento del metodo e offre un'altra opportunità per ripristinare lo stato di un'attività dopo il completamento dell'inizializzazione. &ndash;

Il diagramma seguente illustra il modo in cui vengono usati questi metodi:

[![Diagramma di flusso stati bundle](images/image3-sml.png)](images/image3.png#lightbox)

#### <a name="onsaveinstancestate"></a>OnSaveInstanceState

[OnSaveInstanceState](xref:Android.App.Activity.OnSaveInstanceState*) verrà chiamata quando l'attività viene arrestata. Riceverà un parametro bundle in cui l'attività può archiviare il proprio stato. Quando si verifica una modifica della configurazione di un dispositivo, un'attività `Bundle` può utilizzare l'oggetto passato per mantenere lo stato dell'attività eseguendo l' `OnSaveInstanceState`override di. Si consideri il codice di esempio seguente:

```csharp
int c;

protected override void OnCreate (Bundle bundle)
{
  base.OnCreate (bundle);

  this.SetContentView (Resource.Layout.SimpleStateView);

  var output = this.FindViewById<TextView> (Resource.Id.outputText);

  if (bundle != null) {
    c = bundle.GetInt ("counter", -1);
  } else {
    c = -1;
  }

  output.Text = c.ToString ();

  var incrementCounter = this.FindViewById<Button> (Resource.Id.incrementCounter);

  incrementCounter.Click += (s,e) => {
    output.Text = (++c).ToString();
  };
}
```

Il codice precedente incrementa un numero intero denominato `c` quando si fa clic `incrementCounter` su un pulsante denominato, visualizzando il risultato in `TextView` un `output`oggetto denominato. Quando viene apportata una modifica alla configurazione, ad esempio quando il dispositivo viene ruotato, il codice precedente perderà `c` il valore `bundle` di perché `null`sarebbe, come illustrato nella figura seguente:

[![La visualizzazione non Mostra il valore precedente](images/07-sml.png)](images/07.png#lightbox)

Per mantenere il valore di `c` in questo esempio, è possibile eseguire l' `OnSaveInstanceState`override dell'attività, salvando il valore nel bundle come illustrato di seguito:

```csharp
protected override void OnSaveInstanceState (Bundle outState)
{
  outState.PutInt ("counter", c);
  base.OnSaveInstanceState (outState);
}
```

Ora, quando il dispositivo viene ruotato in un nuovo orientamento, il numero intero viene salvato nel bundle e viene recuperato con la riga:

```csharp
c = bundle.GetInt ("counter", -1);
```

> [!NOTE]
> È importante chiamare sempre l'implementazione di base di `OnSaveInstanceState` in modo che sia possibile salvare anche lo stato della gerarchia di visualizzazione.

##### <a name="view-state"></a>Stato di visualizzazione

L'override `OnSaveInstanceState` di è un meccanismo appropriato per salvare i dati temporanei in un'attività tra le modifiche dell'orientamento, ad esempio il contatore nell'esempio precedente. Tuttavia, l'implementazione predefinita di `OnSaveInstanceState` si occuperà di salvare i dati temporanei nell'interfaccia utente per ogni visualizzazione, purché a ogni visualizzazione sia assegnato un ID. Si immagini, ad esempio, che un' `EditText` applicazione disponga di un elemento definito in XML, come indicato di seguito:

```xml
<EditText android:id="@+id/myText"
  android:layout_width="fill_parent"
  android:layout_height="wrap_content"/>
```

Poiché il `EditText` controllo ha un `id` assegnato, quando l'utente immette alcuni dati e ruota il dispositivo, i dati vengono comunque visualizzati, come illustrato di seguito:

[![I dati vengono conservati in modalità orizzontale](images/08-sml.png)](images/08.png#lightbox)

#### <a name="onrestoreinstancestate"></a>OnRestoreInstanceState

[OnRestoreInstanceState](xref:Android.App.Activity.OnRestoreInstanceState*) verrà chiamato dopo `OnStart`. Fornisce a un'attività la possibilità di ripristinare qualsiasi stato salvato in precedenza in un bundle durante la precedente `OnSaveInstanceState`. Si tratta dello stesso bundle fornito a `OnCreate`.

Il codice seguente dimostra come è possibile ripristinare lo stato `OnRestoreInstanceState`in:

```csharp
protected override void OnRestoreInstanceState(Bundle savedState)
{
    base.OnRestoreSaveInstanceState(savedState);
    var myString = savedState.GetString("myString");
    var myBool = savedState.GetBoolean("myBool");
}
```

Questo metodo esiste per offrire una certa flessibilità quando lo stato deve essere ripristinato. A volte è più appropriato attendere fino a quando non vengono eseguite tutte le inizializzazioni prima di ripristinare lo stato dell'istanza. Inoltre, una sottoclasse di un'attività esistente può voler ripristinare solo determinati valori dallo stato dell'istanza. In molti casi, non è necessario eseguire l'override `OnRestoreInstanceState`di, perché la maggior parte delle attività può ripristinare lo stato `OnCreate`usando il bundle fornito a.

Per un esempio di salvataggio dello stato usando `Bundle`un, vedere la [procedura dettagliata: salvataggio dello stato dell'attività](saving-state.md).

#### <a name="bundle-limitations"></a>Limitazioni del bundle

Sebbene `OnSaveInstanceState` consenta di salvare facilmente i dati temporanei, presenta alcune limitazioni:

- Non viene chiamato in tutti i casi. Se ad esempio si preme **Home** o **indietro** per uscire da un'attività, la `OnSaveInstanceState` chiamata non verrà eseguita.

- Il bundle passato in `OnSaveInstanceState` non è progettato per oggetti di grandi dimensioni, ad esempio immagini. Nel caso di oggetti di grandi dimensioni, è preferibile salvare l'oggetto da [OnRetainNonConfigurationInstance](xref:Android.App.Activity.OnRetainNonConfigurationInstance) , come descritto di seguito.

- I dati salvati usando il bundle vengono serializzati, il che può causare ritardi.

Lo stato del bundle è utile per i dati semplici che non utilizzano molta memoria, mentre *i dati dell'istanza non di configurazione* sono utili per dati più complessi o per dati costosi da recuperare, ad esempio da una chiamata al servizio Web o da una query di database complessa. I dati dell'istanza non di configurazione vengono salvati in un oggetto in base alle esigenze. Nella sezione successiva viene `OnRetainNonConfigurationInstance` introdotto come metodo per mantenere i tipi di dati più complessi tramite le modifiche alla configurazione.

### <a name="persisting-complex-data"></a>Salvataggio permanente dei dati complessi

Oltre a salvare in modo permanente i dati nel bundle, Android supporta anche il salvataggio dei dati tramite l'override di [OnRetainNonConfigurationInstance](xref:Android.App.Activity.OnRetainNonConfigurationInstance) e la `Java.Lang.Object` restituzione di un'istanza di un che contiene i dati da salvare in modo permanente. Per salvare lo stato, è possibile `OnRetainNonConfigurationInstance` usare due vantaggi principali:

- L'oggetto restituito da `OnRetainNonConfigurationInstance` garantisce prestazioni ottimali con tipi di dati più grandi e complessi, perché la memoria mantiene questo oggetto.

- Il `OnRetainNonConfigurationInstance` metodo viene chiamato su richiesta e solo quando necessario. Questa operazione è più economica rispetto all'uso di una cache manuale.

L' `OnRetainNonConfigurationInstance` uso di è adatto per scenari in cui è costoso recuperare più volte i dati, ad esempio nelle chiamate al servizio Web. Si consideri, ad esempio, il codice seguente che cerca Twitter:

```csharp
public class NonConfigInstanceActivity : ListActivity
{
  protected override void OnCreate (Bundle bundle)
  {
    base.OnCreate (bundle);
    SearchTwitter ("xamarin");
  }

  public void SearchTwitter (string text)
  {
    string searchUrl = String.Format("http://search.twitter.com/search.json?" + "q={0}&rpp=10&include_entities=false&" + "result_type=mixed", text);

    var httpReq = (HttpWebRequest)HttpWebRequest.Create (new Uri (searchUrl));
    httpReq.BeginGetResponse (new AsyncCallback (ResponseCallback), httpReq);
  }

  void ResponseCallback (IAsyncResult ar)
  {
    var httpReq = (HttpWebRequest)ar.AsyncState;

    using (var httpRes = (HttpWebResponse)httpReq.EndGetResponse (ar)) {
      ParseResults (httpRes);
    }
  }

  void ParseResults (HttpWebResponse httpRes)
  {
    var s = httpRes.GetResponseStream ();
    var j = (JsonObject)JsonObject.Load (s);

    var results = (from result in (JsonArray)j ["results"] let jResult = result as JsonObject select jResult ["text"].ToString ()).ToArray ();

    RunOnUiThread (() => {
      PopulateTweetList (results);
    });
  }

  void PopulateTweetList (string[] results)
  {
    ListAdapter = new ArrayAdapter<string> (this, Resource.Layout.ItemView, results);
  }
}
```

Questo codice recupera i risultati dal Web formattato come JSON, li analizza e quindi Visualizza i risultati in un elenco, come illustrato nello screenshot seguente:

[![Risultati visualizzati sullo schermo](images/06-sml.png)](images/06.png#lightbox)

Quando viene apportata una modifica alla configurazione, ad esempio quando un dispositivo viene ruotato, il codice ripete il processo. Per riutilizzare i risultati recuperati in origine e non provocare chiamate di rete ridondanti, è `OnRetainNonconfigurationInstance` possibile utilizzare per salvare i risultati, come illustrato di seguito:

```csharp
public class NonConfigInstanceActivity : ListActivity
{
  TweetListWrapper _savedInstance;

  protected override void OnCreate (Bundle bundle)
  {
    base.OnCreate (bundle);

    var tweetsWrapper = LastNonConfigurationInstance as TweetListWrapper;

    if (tweetsWrapper != null) {
      PopulateTweetList (tweetsWrapper.Tweets);
    } else {
      SearchTwitter ("xamarin");
    }

    public override Java.Lang.Object OnRetainNonConfigurationInstance ()
    {
      base.OnRetainNonConfigurationInstance ();
      return _savedInstance;
    }

    ...

    void PopulateTweetList (string[] results)
    {
      ListAdapter = new ArrayAdapter<string> (this, Resource.Layout.ItemView, results);
      _savedInstance = new TweetListWrapper{Tweets=results};
    }
}
```

Ora, quando il dispositivo viene ruotato, i risultati originali vengono recuperati `LastNonConfiguartionInstance` dalla proprietà. In questo esempio, i risultati sono costituiti `string[]` da un tweet che li contiene. Poiché `OnRetainNonConfigurationInstance` richiede che venga `Java.Lang.Object` restituito un oggetto, `string[]` l'oggetto viene sottoposta a `Java.Lang.Object`incapsulamento in una classe di sottoclassi, come illustrato di seguito:

```csharp
class TweetListWrapper : Java.Lang.Object
{
  public string[] Tweets { get; set; }
}
```

Ad esempio, se si tenta di utilizzare `TextView` un oggetto come l'oggetto `OnRetainNonConfigurationInstance` restituito da, l'attività verrà persa, come illustrato nel codice seguente:

```csharp
TextView _textView;

protected override void OnCreate (Bundle bundle)
{
  base.OnCreate (bundle);

  var tv = LastNonConfigurationInstance as TextViewWrapper;

  if(tv != null) {
    _textView = tv;
    var parent = _textView.Parent as FrameLayout;
    parent.RemoveView(_textView);
  } else {
    _textView = new TextView (this);
    _textView.Text = "This will leak.";
  }

  SetContentView (_textView);
}

public override Java.Lang.Object OnRetainNonConfigurationInstance ()
{
  base.OnRetainNonConfigurationInstance ();
  return _textView;
}
```

In questa sezione è stato illustrato come mantenere i `Bundle`dati di stato semplici con e come mantenere i tipi di dati più complessi con. `OnRetainNonConfigurationInstance`

## <a name="summary"></a>Riepilogo

Il ciclo di vita dell'attività Android fornisce un Framework potente per la gestione dello stato delle attività all'interno di un'applicazione, ma può essere difficile da comprendere e implementare. In questo capitolo sono stati introdotti i diversi Stati che un'attività può attraversare durante la sua durata, oltre ai metodi del ciclo di vita associati a tali Stati. Successivamente, è stato fornito materiale sussidiario per il tipo di logica da eseguire in ognuno di questi metodi.

## <a name="related-links"></a>Collegamenti correlati

- [Gestione della rotazione](~/android/app-fundamentals/handling-rotation.md)
- [Attività Android](xref:Android.App.Activity)
