---
title: Ciclo di vita dell'attività
description: Le attività sono un blocco predefinito fondamentale di applicazioni Android e possono essere presenti in un numero di stati diversi. Il ciclo di vita di attività inizia con la creazione di istanze e termina con la distruzione e include molti stati intermedi. Quando un'attività Cambia stato, viene chiamato il metodo di eventi del ciclo di vita appropriata, notificando l'attività dell'imminente modifica dello stato e in modo che possa eseguire il codice per adattarsi a tale modifica. Questo articolo esamina il ciclo di vita delle attività e spiega le responsabilità che un'attività ha durante ognuna di queste modifiche di stato da parte di un'applicazione affidabile, ben progettata.
ms.prod: xamarin
ms.assetid: 05B34788-F2D2-4347-B66B-40AFD7B1D167
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/28/2018
ms.openlocfilehash: 48ff30397b2592dd2c4dbd445987392d78ced6f3
ms.sourcegitcommit: d3f48bfe72bfe03aca247d47bc64bfbfad1d8071
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/06/2019
ms.locfileid: "66740773"
---
# <a name="activity-lifecycle"></a>Ciclo di vita dell'attività

_Le attività sono un blocco predefinito fondamentale di applicazioni Android e possono essere presenti in un numero di stati diversi. Il ciclo di vita di attività inizia con la creazione di istanze e termina con la distruzione e include molti stati intermedi. Quando un'attività Cambia stato, viene chiamato il metodo di eventi del ciclo di vita appropriata, notificando l'attività dell'imminente modifica dello stato e in modo che possa eseguire il codice per adattarsi a tale modifica. Questo articolo esamina il ciclo di vita delle attività e spiega le responsabilità che un'attività ha durante ognuna di queste modifiche di stato da parte di un'applicazione affidabile, ben progettata._

## <a name="activity-lifecycle-overview"></a>Panoramica del ciclo di vita dell'attività

Le attività sono un concetto di programmazione insolito specifico di Android. Nello sviluppo di applicazioni tradizionali viene in genere presente un metodo main statico, che viene eseguito per avviare l'applicazione. In Android, tuttavia, le cose sono diverse; Le applicazioni Android possono essere avviate tramite qualsiasi attività registrati all'interno di un'applicazione. In pratica, la maggior parte delle applicazioni disporrà solo un'attività specifica che viene specificata come punto di ingresso dell'applicazione. Tuttavia, se si blocca, o viene terminata dal sistema operativo, il sistema operativo può provare a riavviare l'applicazione nell'ultima attività aperta o in qualsiasi altro all'interno dello stack attività precedente.
Inoltre, il sistema operativo potrebbe sospendere le attività quando non sono attivi e recuperarli se è disponibile memoria sufficiente in. Per consentire all'applicazione ripristinare correttamente il proprio stato nel caso in cui un'attività viene riavviata, soprattutto se che attività dipende dai dati dalle attività precedenti, è necessario effettuare un'attenta valutazione.

Il ciclo di vita di attività viene implementata come una raccolta di chiamate di metodi del sistema operativo in tutto il ciclo di vita di un'attività. Questi metodi consentono agli sviluppatori di implementare la funzionalità che è necessaria soddisfare i requisiti di gestione dello stato e delle risorse delle applicazioni.

È estremamente importante per lo sviluppatore dell'applicazione analizzare i requisiti di ogni attività per determinare quali metodi esposti dal ciclo di vita delle attività devono essere implementati. In caso contrario possa causare instabilità dell'applicazione, gli arresti anomali, bloat resource e possibilmente anche sottostante l'instabilità del sistema operativo.

In questo capitolo esamina il ciclo di vita delle attività in modo dettagliato, tra cui:

-  Stati di attività
-  Metodi del ciclo di vita
-  Mantenendo lo stato di un'applicazione


In questa sezione include anche un [walkthrough](~/android/app-fundamentals/activity-lifecycle/saving-state.md) che forniscono esempi pratici su come salvare in modo efficiente lo stato durante il ciclo di vita di attività. Al termine di questo capitolo è necessario comprendere il ciclo di vita di attività e come supportarlo in un'applicazione Android.

## <a name="activity-lifecycle"></a>Ciclo di vita dell'attività

Il ciclo di vita di attività Android è costituito da una raccolta di metodi esposti all'interno della classe di attività che forniscono allo sviluppatore di un framework di gestione risorse. Questo framework consente agli sviluppatori di soddisfare i requisiti di gestione di stato univoco di ogni attività all'interno di un'applicazione e gestire correttamente la gestione delle risorse.

### <a name="activity-states"></a>Stati di attività

Il sistema operativo Android esegue l'arbitraggio attività basata sul relativo stato. Ciò consente di identificare le attività che non sono più in uso, che consente di recuperare risorse di memoria e il sistema operativo Android. Il diagramma seguente illustra gli stati di che un'attività può attraversare tutta la sua durata:

[![Diagramma degli stati di attività](images/image1-sml.png)](images/image1.png#lightbox)

Questi stati possono essere suddivisi in 4 gruppi principali, come indicato di seguito:

1.  *Attiva o in esecuzione* &ndash; le attività vengono considerate attivi o in esecuzione se si trovano in primo piano, note anche come l'inizio dello stack di attività. Ciò viene considerato l'attività con priorità più alta in Android e di conseguenza verrà solo terminata dal sistema operativo in condizioni estreme, ad esempio come se l'attività tenta di utilizzare più memoria è disponibile nel dispositivo perché ciò potrebbe causare l'interfaccia utente smette di rispondere.

1.  *In pausa* &ndash; quando il dispositivo va in sospensione o un'attività è ancora visibile ma parzialmente nascosti da un'attività di nuovo, non-ingrandita o trasparente, l'attività viene considerata in pausa. Attività sospesa sono ancora attive, vale a dire mantenere tutte le informazioni sullo stato del membro e resta collegati per la gestione finestre. Questo è considerato la seconda attività con priorità più elevata in Android e, pertanto verrà terminata solo dal sistema operativo se terminare l'attività soddisferà i requisiti di risorse necessari per mantenere le attività attive o in esecuzione, stabile e reattive.

1.  *Arrestato/Backgrounded* &ndash; le attività che sono completamente nascosti da un'altra attività vengono considerate interrotto o in background.
    Attività arrestata comunque provare a mantenere le informazioni di stato e il membro per fino a quando le attività possibili, ma arrestate sono considerate la priorità più bassa dei tre stati e, di conseguenza, il sistema operativo termina le attività in questo stato per soddisfare la risorsa requisiti di attività con priorità superiore.

1.  *Riavviato* &ndash; è possibile che un'attività che varia da in pausa su arrestato nel ciclo di vita per essere rimossa dalla memoria da Android. Se l'utente passa indietro per l'attività deve essere riavviato, ripristinarne lo stato salvato in precedenza e quindi visualizzati all'utente.


### <a name="activity-re-creation-in-response-to-configuration-changes"></a>La ricreazione di attività in risposta alle modifiche di configurazione

Per rendere più complicate è rilevante, Android genera una chiave inglese altre nella combinazione di chiamato le modifiche alla configurazione. Modifiche di configurazione sono attività rapido distruzione di $ / Ripeti-creation cicli che si verificano quando viene modificata la configurazione di un'attività, ad esempio quando il dispositivo viene [ruotata](~/android/app-fundamentals/handling-rotation.md) (e l'attività deve ottenere ricompilato in orizzontale o verticale modalità), quando viene visualizzata la tastiera e l'attività viene presentata l'opportunità di ridimensionamento automatico, o quando il dispositivo viene inserito in un ancoraggio, tra gli altri.

Le modifiche alla configurazione causano comunque le stesse modifiche di stato dell'attività che si verificano durante l'arresto e riavvio di un'attività. Tuttavia, per fare in modo che un'applicazione ritiene reattiva ed esegue anche durante le modifiche alla configurazione, è importante che vengano gestiti più rapidamente possibile. Per questo motivo, Android offre un'API specifica che può essere utilizzata per rendere persistente lo stato durante le modifiche alla configurazione.
Si affronterà questo aspetto in un secondo momento le [la gestione dello stato in tutto il ciclo di vita](~/android/app-fundamentals/activity-lifecycle/index.md#Managing_State_Throughout_the_Lifecycle) sezione.

### <a name="activity-lifecycle-methods"></a>Metodi del ciclo di vita di attività

Android SDK e, di conseguenza, il framework di xamarin. Android offre un potente modello per la gestione dello stato delle attività all'interno di un'applicazione. Quando viene modificato lo stato dell'attività, l'attività viene tenuto aggiornato tramite il sistema operativo, che chiama i metodi specifici su tale attività. Il diagramma seguente illustra questi metodi in relazione al ciclo di vita di attività:

[![Diagramma di flusso di attività del ciclo di vita](images/image2-sml.png)](images/image2.png#lightbox)

Gli sviluppatori, è possibile gestire le modifiche dello stato eseguendo l'override di questi metodi all'interno di un'attività. È importante tenere presente tuttavia che tutti i metodi del ciclo di vita vengono chiamati sul thread UI e bloccano il sistema operativo di eseguire la parte successiva di lavoro dell'interfaccia utente, ad esempio nascondendo l'attività corrente, la visualizzazione di una nuova attività e così via. Di conseguenza, codice in questi metodi deve essere più breve possibile rendere un'applicazione è possibile anche eseguire. Qualsiasi attività con esecuzione prolungata deve essere eseguita in un thread in background.

Esaminiamo ciascuno di questi metodi del ciclo di vita e il loro utilizzo:

#### <a name="oncreate"></a>OnCreate

[OnCreate](https://developer.xamarin.com/api/member/Android.App.Activity.OnCreate/p/Android.OS.Bundle/) è il primo metodo da chiamare quando viene creata un'attività.
`OnCreate` viene sempre sottoposto a override per eseguire qualsiasi inizializzazioni di avvio che possono essere richiesto da un'attività, ad esempio:

-  Creazione di viste
-  Inizializzazione di variabili
-  Associazione di dati statici agli elenchi


`OnCreate` accetta una [Bundle](https://developer.xamarin.com/api/type/Android.OS.Bundle/) parametro, ovvero un dizionario per l'archiviazione e il passaggio di oggetti e le informazioni sullo stato tra attività se il pacchetto non è null, questo indica l'attività sta riavviando e deve ripristinare il relativo stato dal istanza precedente. Il codice seguente illustra come recuperare i valori dal bundle:

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

Una volta `OnCreate` ha completato, verrà chiamato Android `OnStart`.

#### <a name="onstart"></a>OnStart

[OnStart](https://developer.xamarin.com/api/member/Android.App.Activity.OnStart/) viene sempre chiamato dal sistema dopo `OnCreate` viene completata. Le attività possono eseguire l'override di questo metodo se è necessario eseguire alcun diritto di specifiche attività prima che un'attività sia visibile come aggiornare i valori correnti delle visualizzazioni all'interno dell'attività. Android chiamerà `OnResume` immediatamente dopo questo metodo.

#### <a name="onresume"></a>OnResume

Le chiamate di sistema [OnResume](https://developer.xamarin.com/api/member/Android.App.Activity.OnResume/) quando l'attività è pronto per iniziare a interagire con l'utente.
Le attività devono eseguire l'override di questo metodo per eseguire attività quali:

-  Imparare a frequenze di fotogrammi (attività comuni nello sviluppo di giochi)
-  Avviare le animazioni
-  In attesa di aggiornamenti GPS
-  Visualizzare gli avvisi pertinenti o le finestre di dialogo
-  Collegare i gestori di eventi esterni


Ad esempio, il frammento di codice seguente viene illustrato come inizializzare la fotocamera:

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

`OnResume` è importante perché qualsiasi operazione che richiede solo pochi `OnPause` deve essere annullato in `OnResume`, dal momento che è l'unico metodo del ciclo di vita che viene sempre eseguito dopo `OnPause` quando si attiva l'attività al ciclo di vita.

#### <a name="onpause"></a>OnPause

[OnPause](https://developer.xamarin.com/api/member/Android.App.Activity.OnPause/) viene chiamato quando il sistema sta per inserire l'attività in background o quando l'attività diventa parzialmente oscurata. Le attività devono eseguire l'override di questo metodo se è necessario:

-   Eseguire il commit dei dati persistenti le modifiche non salvate

-   Eliminare o liberare altri oggetti l'utilizzo di risorse

-   Ramp verso il basso frequenze dei fotogrammi e animazioni sospensione

-   Annullare la registrazione di gestori di eventi esterni o gestori di notifica (ad esempio quelle che sono associate a un servizio). Questa operazione deve essere eseguita per evitare perdite di memoria di attività.

-   Analogamente, se l'attività ha visualizzati avvisi o finestre di dialogo, è necessario rimuovere con il `.Dismiss()` (metodo).

Ad esempio, il seguente frammento di codice rilascerà la fotocamera, come l'attività non può apportare utilizzarlo durante la pausa:

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

Esistono due metodi del ciclo di vita possibili che verranno chiamati dopo `OnPause`:

1.  `OnResume` verrà chiamato se l'attività deve essere restituita in primo piano.
1.  `OnStop` viene chiamato se l'attività viene emesso in background.


#### <a name="onstop"></a>OnStop

[OnStop](https://developer.xamarin.com/api/member/Android.App.Activity.OnStop/) viene chiamato quando l'attività non è più visibile all'utente. Ciò si verifica quando si verifica una delle operazioni seguenti:

-  Una nuova attività è in corso l'avvio e sta coprendo di questa attività.
-  Un'attività esistente verrà presto portata in primo piano.
-  L'attività viene eliminata definitivamente.


`OnStop` può non sempre essere chiamato in situazioni di memoria insufficiente, ad esempio quando Android richiede un utilizzo elevato delle risorse e l'attività non è correttamente in background. Per questo motivo, è consigliabile non fare affidamento su `OnStop` introduzione chiamato durante la preparazione di un'attività da eliminare. I metodi del ciclo di vita successivi che possono essere chiamati dopo che questa verrà `OnDestroy` se l'attività è deprecata o `OnRestart` se l'attività proveniente per interagire con l'utente.

#### <a name="ondestroy"></a>OnDestroy

[OnDestroy](https://developer.xamarin.com/api/member/Android.App.Activity.OnDestroy/) è il metodo finale che viene chiamato su un'istanza di attività prima che ha eliminato definitivamente e completamente rimossa dalla memoria. In condizioni estreme Android potrebbe terminare il processo di applicazione che ospita l'attività, e di conseguenza `OnDestroy` non richiamato. La maggior parte delle attività non verrà implementata in questo metodo perché la maggior parte la pulizia e chiusura è stata effettuata nel `OnPause` e `OnStop` metodi. Il `OnDestroy` è in genere sottoposto a override per pulire prolungata che eseguono le risorse che potrà verificarsi perdite di risorse. Un esempio di questo potrebbe essere il thread in background avviati nella `OnCreate`.

Non sarà presente alcun metodo del ciclo di vita viene chiamato dopo che l'attività è stata eliminata definitivamente.

#### <a name="onrestart"></a>OnRestart

[OnRestart](https://developer.xamarin.com/api/member/Android.App.Activity.OnRestart/) viene chiamato dopo che l'attività è stato arrestato, prima viene avviata nuovamente. Un buon esempio di questo sarebbe quando l'utente preme il pulsante home mentre su un'attività nell'applicazione. In questo caso `OnPause` e quindi `OnStop` vengono chiamati i metodi e l'attività viene spostata in background ma non viene eliminato. Se l'utente fosse per ripristinare l'applicazione utilizzando Gestione attività o un'applicazione simile, Android chiamerà il `OnRestart` metodo dell'attività.

Non esistono regole generali per il tipo di logica deve essere implementato in `OnRestart`. Infatti `OnStart` viene sempre richiamato indipendentemente dal fatto che l'attività viene creato o viene riavviato, in modo che eventuali risorse necessarie per l'attività devono essere inizializzate nel `OnStart`, anziché `OnRestart`.

Il metodo del ciclo di vita successivo viene chiamato dopo `OnRestart` saranno `OnStart`.

### <a name="back-vs-home"></a>Eseguire il backup di Visual Studio. Home

Numero di dispositivi Android hanno due pulsanti distinti: un pulsante "Indietro" e un pulsante "Home". Un esempio di questo può essere visualizzato nello screenshot seguente di Android è 4.0.3:

[![Pulsanti Indietro e Home](images/image4-sml.png)](images/image4.png#lightbox)

È presente una sottile differenza tra i due pulsanti, anche se sembrano avere lo stesso effetto dell'inserimento di un'applicazione in background. Quando un utente fa clic sul pulsante Indietro, si indica a Android che hanno terminato l'attività. Android comporta l'eliminazione dell'attività. Al contrario, quando l'utente fa clic sul pulsante Home l'attività è semplicemente inserito lo sfondo &ndash; Android non comporterà la terminazione dell'attività.

<a name="Managing_State_Throughout_the_Lifecycle" />

## <a name="managing-state-throughout-the-lifecycle"></a>La gestione dello stato nel ciclo di vita

Quando un'attività è stata arrestata o eliminato definitivamente il sistema fornisce un'opportunità per salvare lo stato dell'attività per la riattivazione più avanti.
In questo stato salvato è detto lo stato dell'istanza. Android sono disponibili tre opzioni per l'archiviazione dello stato dell'istanza durante il ciclo di vita di attività:

1. L'archiviazione di valori primitivi in un `Dictionary` noto come un [Bundle](https://developer.xamarin.com/api/type/Android.OS.Bundle/) che Android userà per salvare lo stato.

1. Creazione di una classe personalizzata che conterrà valori complessi come le bitmap. Android userà questa classe personalizzata per salvare lo stato.

1. Aggirando il ciclo di vita di modifiche di configurazione e si assume la responsabilità completa per mantenere lo stato dell'attività.


Questa guida illustra le prime due opzioni.



### <a name="bundle-state"></a>Stato pacchetto

L'opzione primario per il salvataggio dello stato dell'istanza consiste nell'usare un oggetto dizionario chiave-valore noto come un [Bundle](https://developer.xamarin.com/api/type/Android.OS.Bundle/).
Si tenga presente che quando viene creata un'attività che il `OnCreate` viene passato un bundle come parametro, questo pacchetto può essere utilizzato per ripristinare lo stato dell'istanza. Non è consigliabile usare un bundle di dati più complessi che non verranno rapidamente o serializzano facilmente per (ad esempio le bitmap), coppie chiave/valore invece, deve usarlo per valori semplici, ad esempio le stringhe.

Un'attività fornisce i metodi per facilitare il salvataggio e il recupero dello stato dell'istanza nel Bundle:

-   [OnSaveInstanceState](https://developer.xamarin.com/api/member/Android.App.Activity.OnSaveInstanceState/p/Android.OS.Bundle/) &ndash; viene richiamato da Android quando l'attività viene eliminata definitivamente. Le attività possono implementare questo metodo se è necessario rendere persistenti tutti gli elementi dello stato di chiave/valore.

-   [OnRestoreInstanceState](https://developer.xamarin.com/api/member/Android.App.Activity.OnRestoreInstanceState/p/Android.OS.Bundle/) &ndash; ciò viene chiamato dopo il `OnCreate` metodo viene completato e fornisce un'altra opportunità per un'attività per ripristinarne lo stato al termine dell'inizializzazione.

Il diagramma seguente illustra come questi metodi vengono usati:

[![Diagramma di flusso stati bundle](images/image3-sml.png)](images/image3.png#lightbox)

#### <a name="onsaveinstancestate"></a>OnSaveInstanceState

[OnSaveInstanceState](https://developer.xamarin.com/api/member/Android.App.Activity.OnSaveInstanceState/p/Android.OS.Bundle/) verrà richiamato quando l'attività è in corso l'arresto. Riceverà un parametro di bundle che l'attività può archiviare lo stato in. Quando un dispositivo si verifica una modifica della configurazione, un'attività è possibile usare la `Bundle` oggetto passato per mantenere lo stato dell'attività eseguendo l'override `OnSaveInstanceState`. Si consideri il codice di esempio seguente:

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

Il codice riportato sopra viene incrementato di un numero intero denominato `c` quando un pulsante denominato `incrementCounter` viene fatto clic, visualizzazione del risultato in un `TextView` denominato `output`. Quando si verifica una modifica della configurazione, ad esempio, quando il dispositivo viene ruotato: il codice sopra riportato si perderebbe hodnotou `c` perché il `bundle` sarebbe `null`, come illustrato nella figura seguente:

[![Non viene visualizzato il valore precedente](images/07-sml.png)](images/07.png#lightbox)

Per mantenere il valore della `c` in questo esempio, è possibile eseguire l'override dell'attività `OnSaveInstanceState`, salvando il valore nel bundle, come illustrato di seguito:

```csharp
protected override void OnSaveInstanceState (Bundle outState)
{
  outState.PutInt ("counter", c);
  base.OnSaveInstanceState (outState);
}
```

Ora quando il dispositivo viene ruotato in un nuovo orientamento, l'intero viene salvato nel bundle e verrà recuperato con la riga:

```csharp
c = bundle.GetInt ("counter", -1);
```

> [!NOTE]
> È importante sempre chiamata l'implementazione di base di `OnSaveInstanceState` in modo che lo stato della gerarchia di visualizzazione può anche essere salvato.



##### <a name="view-state"></a>Lo stato di visualizzazione

Si esegue l'override `OnSaveInstanceState` è un meccanismo appropriato per il salvataggio dei dati temporanei in un'attività tra le modifiche apportate orientamento, ad esempio il contatore nell'esempio precedente. Tuttavia, l'implementazione predefinita di `OnSaveInstanceState` si occuperà di salvataggio dei dati temporanei nell'interfaccia utente per ogni visualizzazione, purché ogni visualizzazione dispone di un ID assegnato. Ad esempio, un'applicazione ha un `EditText` elemento definito in XML, come indicato di seguito:

```xml
<EditText android:id="@+id/myText"
  android:layout_width="fill_parent"
  android:layout_height="wrap_content"/>
```

Poiché il `EditText` controllo ha un `id` assegnati, quando l'utente immette alcuni dati e ruota il dispositivo, i dati vengono comunque visualizzati, come illustrato di seguito:

[![I dati vengono conservati in modalità orizzontale](images/08-sml.png)](images/08.png#lightbox)

#### <a name="onrestoreinstancestate"></a>OnRestoreInstanceState

[OnRestoreInstanceState](https://developer.xamarin.com/api/member/Android.App.Activity.OnRestoreInstanceState/p/Android.OS.Bundle/) viene chiamato dopo `OnStart`. Fornisce un'attività la possibilità di ripristinare lo stato di cui è stato salvato in precedenza a un Bundle durante il precedente `OnSaveInstanceState`. Questo è il bundle stesso che ha lo scopo di `OnCreate`, tuttavia.

Il codice seguente viene illustrato come è possibile ripristinare lo stato in `OnRestoreInstanceState`:

```csharp
protected override void OnRestoreInstanceState(Bundle savedState)
{
    base.OnRestoreSaveInstanceState(savedState);
    var myString = savedState.GetString("myString");
    var myBool = savedState.GetBoolean("myBool");
}
```

Questo metodo è disponibile per fornire alcune flessibilità per quanto riguarda quando lo stato deve essere ripristinato. In alcuni casi è preferibile attendere fino a quando non vengono eseguite tutte le inizializzazioni prima di ripristinare lo stato dell'istanza. Inoltre, una sottoclasse di un'attività esistente solo possibile ripristinare determinati valori dallo stato dell'istanza. In molti casi, non è necessario eseguire l'override `OnRestoreInstanceState`, dal momento che la maggior parte delle attività può ripristinare lo stato usando i bundle fornito a `OnCreate`.

Per un esempio di salvataggio stato tramite un `Bundle`, fare riferimento al [questa procedura dettagliata - stato dell'attività di salvataggio](saving-state.md).


#### <a name="bundle-limitations"></a>Limitazioni di aggregazione

Sebbene `OnSaveInstanceState` rende facile salvare i dati temporanei, presenta alcune limitazioni:

-   Non viene chiamato in tutti i casi. Ad esempio, premendo **Home** oppure **nuovamente** per chiudere un'attività non comporterà `OnSaveInstanceState` chiamato.

-   Il bundle passati `OnSaveInstanceState` non è progettato per gli oggetti di grandi dimensioni, ad esempio le immagini. Nel caso di oggetti di grandi dimensioni, il salvataggio dell'oggetto da [OnRetainNonConfigurationInstance](https://developer.xamarin.com/api/member/Android.App.Activity.OnRetainNonConfigurationInstance/) è preferibile, come descritto di seguito.

-   I dati salvati utilizzando l'aggregazione viene serializzati, che può causare ritardi.

Lo stato di bundle è utile per i dati semplici che non utilizzano una quantità eccessiva di memoria, mentre *i dati dell'istanza non relativi alla configurazione* è utile per i dati più complessi o dati che è dispendioso recuperare, ad esempio da una chiamata al servizio web o un complicato query di database. I dati dell'istanza di configurazione non vengono salvati in un oggetto in base alle esigenze. La sezione successiva viene introdotta `OnRetainNonConfigurationInstance` allo scopo di preservare i tipi di dati più complessi tramite modifiche di configurazione.


### <a name="persisting-complex-data"></a>Rendere persistenti i dati complessi

Oltre a rendere persistenti i dati presenti nel bundle, Android supporta anche il salvataggio dei dati eseguendo l'override [OnRetainNonConfigurationInstance](https://developer.xamarin.com/api/member/Android.App.Activity.OnRetainNonConfigurationInstance/) e la restituzione di un'istanza di un `Java.Lang.Object` che contiene i dati in modo permanente. Esistono due vantaggi principali dell'uso `OnRetainNonConfigurationInstance` per salvare lo stato:

-   L'oggetto restituito da `OnRetainNonConfigurationInstance` funziona bene con i tipi di dati più grandi e complesse poiché memoria consente di mantenere questo oggetto.

-   Il `OnRetainNonConfigurationInstance` metodo viene chiamato su richiesta e solo quando necessario. Questo è più conveniente rispetto all'uso di una cache manuale.

Usando `OnRetainNonConfigurationInstance` è adatto per scenari in cui è costoso recuperare i dati più volte, ad esempio chiamate al servizio web. Ad esempio, si consideri il codice seguente che cerca Twitter:

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

Questo codice recupera i risultati dal web formattati come JSON, li analizza e quindi presenta i risultati in un elenco, come illustrato nello screenshot seguente:

[![Risultati visualizzati sullo schermo](images/06-sml.png)](images/06.png#lightbox)

Quando una modifica della configurazione si verifica, ad esempio, quando un dispositivo viene ruotato - il codice ripete il processo. Per riutilizzare i risultati recuperati originariamente e non causano le chiamate di rete superflue e ridondante, è possibile usare `OnRetainNonconfigurationInstance` per salvare i risultati, come illustrato di seguito:

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

A questo punto quando il dispositivo viene ruotato, i risultati originali vengono recuperati dal `LastNonConfiguartionInstance` proprietà. In questo esempio, i risultati costituiti da un `string[]` contenente i TWEET. Poiché `OnRetainNonConfigurationInstance` richiede che un `Java.Lang.Object` da restituire, il `string[]` viene eseguito il wrapping in una classe che rappresenti le sottoclassi `Java.Lang.Object`, come illustrato di seguito:

```csharp
class TweetListWrapper : Java.Lang.Object
{
  public string[] Tweets { get; set; }
}
```

Ad esempio, il tentativo di utilizzare un `TextView` dell'oggetto restituito da `OnRetainNonConfigurationInstance` perdono l'attività, come illustrato nel codice seguente:

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

In questa sezione, si è appreso a conservare i dati di stato semplici con il `Bundle`, e mantenere i tipi di dati più complessi con `OnRetainNonConfigurationInstance`.

## <a name="summary"></a>Riepilogo

Il ciclo di vita di attività Android fornisce un framework potente per la gestione dello stato delle attività all'interno di un'applicazione, ma può essere difficile da comprendere e implementare. In questo capitolo è stato introdotto i diversi stati che possa attraversare un'attività durante la sua durata, nonché i metodi del ciclo di vita che sono associati a tali Stati. Successivamente, è stato fornito materiale sussidiario sulla tipologia di logica deve essere eseguita in ognuno di questi metodi.


## <a name="related-links"></a>Collegamenti correlati

- [Gestione della rotazione](~/android/app-fundamentals/handling-rotation.md)
- [Attività Android](https://developer.xamarin.com/api/type/Android.App.Activity/)
