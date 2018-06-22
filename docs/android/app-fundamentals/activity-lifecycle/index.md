---
title: Ciclo di vita dell'attività
description: Le attività sono un blocco predefinito fondamentale di applicazioni Android e possono essere presenti in un numero di stati diversi. Il ciclo di vita di attività inizia con la creazione di istanze e termina con distruzione e include molti stati intermedi. Quando un'attività viene modificato, viene chiamato il metodo di evento del ciclo di vita appropriato, l'attività dell'imminente modifica lo stato di notifica e in modo che possa eseguire il codice per adattarsi a tale modifica. In questo articolo esamina il ciclo di vita delle attività e illustra la responsabilità che disponga di un'attività durante ognuna di queste modifiche di stato da parte di un'applicazione ben progettata e affidabile.
ms.prod: xamarin
ms.assetid: 05B34788-F2D2-4347-B66B-40AFD7B1D167
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/28/2018
ms.openlocfilehash: f35f3e59d8b669795ade3d370894e45866cea1ff
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2018
ms.locfileid: "30773553"
---
# <a name="activity-lifecycle"></a>Ciclo di vita dell'attività

_Le attività sono un blocco predefinito fondamentale di applicazioni Android e possono essere presenti in un numero di stati diversi. Il ciclo di vita di attività inizia con la creazione di istanze e termina con distruzione e include molti stati intermedi. Quando un'attività viene modificato, viene chiamato il metodo di evento del ciclo di vita appropriato, l'attività dell'imminente modifica lo stato di notifica e in modo che possa eseguire il codice per adattarsi a tale modifica. In questo articolo esamina il ciclo di vita delle attività e illustra la responsabilità che disponga di un'attività durante ognuna di queste modifiche di stato da parte di un'applicazione ben progettata e affidabile._

## <a name="activity-lifecycle-overview"></a>Panoramica del ciclo di vita di attività

Le attività sono un concetto di programmazione insolito specifico di Android. Nello sviluppo di applicazioni tradizionale è in genere un metodo main statico, che viene eseguito per avviare l'applicazione. Con Android, tuttavia, le cose sono diverse; Le applicazioni Android possono essere avviate tramite qualsiasi attività registrati all'interno di un'applicazione. In pratica, la maggior parte delle applicazioni ha solo un'attività specifica che viene specificata come punto di ingresso dell'applicazione. Tuttavia, se un'applicazione si blocca, o viene terminata dal sistema operativo, il sistema operativo è possibile provare a riavviare l'applicazione l'ultima attività aperta o in altri percorsi nello stack attività precedente.
Inoltre, il sistema operativo può sospendere le attività quando non si attiva e recuperarli in caso di memoria insufficiente. Per consentire all'applicazione ripristinare correttamente il relativo stato nel caso in cui un'attività viene riavviata, soprattutto se che attività dipende dai dati dall'attività precedente, è necessario effettuare un'attenta valutazione.

Il ciclo di vita di attività viene implementata come una raccolta di chiamate di metodi, il sistema operativo in tutto il ciclo di vita di un'attività. Questi metodi consentono agli sviluppatori di implementare la funzionalità che è necessaria soddisfare i requisiti di gestione dello stato e delle risorse delle applicazioni.

È estremamente importante per lo sviluppatore dell'applicazione analizzare i requisiti di ogni attività per determinare quali metodi esposti dal ciclo di vita delle attività devono essere implementate. Errore per eseguire questa operazione può causare instabilità dell'applicazione, arresti anomali del sistema, crescita delle risorse e possibilmente anche sottostante instabilità del sistema operativo.

In questo capitolo esamina il ciclo di vita di attività in modo dettagliato, tra cui:

-  Stati di attività
-  Metodi del ciclo di vita
-  Mantenere lo stato di un'applicazione


Questa sezione include anche un [procedura dettagliata](~/android/app-fundamentals/activity-lifecycle/saving-state.md) che forniscono esempi pratici per il salvataggio in modo efficiente di stato durante il ciclo di vita di attività. Entro la fine di questo capitolo è necessario conoscere il ciclo di vita di attività e come per il supporto in un'applicazione Android.

## <a name="activity-lifecycle"></a>Ciclo di vita dell'attività

Il ciclo di vita di attività Android è costituito da una raccolta di metodi esposti all'interno della classe di attività che forniscono allo sviluppatore di un framework di gestione risorse. Questo framework consente agli sviluppatori di soddisfare i requisiti di gestione stato univoco di ogni attività all'interno di un'applicazione e gestire correttamente la gestione delle risorse.

### <a name="activity-states"></a>Stati di attività

Il sistema operativo Android regola basata sul relativo stato di attività. Questo consente di identificare le attività che non sono più in uso, che consente di liberare risorse di memoria e il sistema operativo Android. Il diagramma seguente illustra gli stati di che un'attività può essere eseguita per tutta la durata:

[![Diagramma di stati di attività](images/image1-sml.png)](images/image1.png#lightbox)

Questi stati possono essere suddivisa in 4 gruppi principali, come indicato di seguito:

1.  *Attiva o in esecuzione* &ndash; attività considerate attivo o in esecuzione se sono presenti in primo piano, note anche come l'inizio dello stack di attività. Questo viene considerato l'attività di priorità più elevata in Android e di conseguenza viene solo terminato dal sistema operativo in condizioni estreme, ad esempio come se l'attività tenta di utilizzare più memoria di è disponibile nel dispositivo come ciò potrebbe causare l'interfaccia utente potrebbe non rispondere.

1.  *In pausa* &ndash; quando il dispositivo passa alla modalità sospensione o un'attività è ancora visibile ma parzialmente nascosto da un'attività di nuovo, non a dimensioni intere o trasparente, l'attività viene considerato in pausa. Attività sospesa sono ancora attive, che si sono mantenere tutte le informazioni sullo stato del membro e rimanere connessi alla gestione della finestra. Questo è considerato la seconda attività di priorità più elevata in Android e, come ad esempio, viene terminato solo dal sistema operativo se la terminazione di questa attività verrà soddisfa i requisiti di risorse necessari per mantenere l'attività attiva/esecuzione stabile e dinamico.

1.  *Arresto/Backgrounded* &ndash; sono considerate attività che sono completamente nascosto da un'altra attività interrotta o in background.
    Attività arrestata ancora tenta di mantenere le informazioni di stato e il membro per fino a quando possibile, tuttavia arrestata attività sono considerate come la priorità più bassa dei tre stati e, di conseguenza, il sistema operativo verrà terminare le attività in questo stato per soddisfare la risorsa requisiti delle attività di priorità superiore.

1.  *Riavvio* &ndash; è possibile che un'attività che si trova in un punto qualsiasi in sospeso su arrestato il ciclo di vita per essere rimosso dalla memoria da Android. Se l'utente passa all'attività deve essere riavviata, ripristinare lo stato salvato in precedenza e quindi visualizzati all'utente.


### <a name="activity-re-creation-in-response-to-configuration-changes"></a>Ricreazione di attività in risposta alle modifiche di configurazione

Per rendere questioni più complicati, Android genera una chiave inglese più nella combinazione di modifiche di configurazione denominata. Modifiche alla configurazione sono attività rapido distruzione/rinviati-creation cicli che si verificano quando viene modificata la configurazione di un'attività, ad esempio quando il dispositivo è [ruotata](~/android/app-fundamentals/handling-rotation.md) (e l'attività deve ottenere ricompilato in orizzontale o verticale modalità), quando viene visualizzata la tastiera e l'attività viene presentata l'opportunità di ridimensionamento, o quando il dispositivo viene inserito in un ancoraggio, tra gli altri.

Le modifiche alla configurazione continueranno a determinare le stesse modifiche di stato dell'attività che si verificano durante l'arresto e riavvio di un'attività. Tuttavia, per essere certi che un'applicazione ritiene reattiva ed esegue anche durante la modifica di configurazione, è importante che essi essere gestite più rapidamente possibile. Per questo motivo, Android offre un'API specifica può essere usata per la persistenza dello stato durante la modifica di configurazione.
Tratteremo questo in un secondo momento il [la gestione dello stato in tutto il ciclo di vita](~/android/app-fundamentals/activity-lifecycle/index.md#Managing_State_Throughout_the_Lifecycle) sezione.

### <a name="activity-lifecycle-methods"></a>Metodi del ciclo di vita di attività

Android SDK e, di conseguenza, il framework di xamarin forniscono un modello potente per la gestione dello stato delle attività all'interno di un'applicazione. Quando viene modificato lo stato di un'attività, l'attività di notifica dal sistema operativo, che chiama i metodi specifici per tale attività. Il diagramma seguente illustra questi metodi in relazione al ciclo di vita di attività:

[![Diagramma di flusso di attività del ciclo di vita](images/image2-sml.png)](images/image2.png#lightbox)

Gli sviluppatori, è possibile gestire le modifiche dello stato eseguendo l'override di questi metodi all'interno di un'attività. È importante notare tuttavia che tutti i metodi del ciclo di vita vengono chiamati nel thread UI e bloccano il sistema operativo di eseguire la parte successiva operazioni dell'interfaccia utente, ad esempio nascondere l'attività corrente, la visualizzazione di una nuova attività e così via. Di conseguenza, codice in questi metodi deve essere più breve possibile eseguire un'applicazione ritiene prestazioni. Qualsiasi attività a esecuzione prolungata devono essere eseguite in un thread in background.

Si esamineranno ognuno di questi metodi del ciclo di vita e il relativo utilizzo:

#### <a name="oncreate"></a>OnCreate

[OnCreate](https://developer.xamarin.com/api/member/Android.App.Activity.OnCreate/p/Android.OS.Bundle/) è il primo metodo da chiamare quando viene creata un'attività.
`OnCreate` viene sempre sottoposto a override per eseguire qualsiasi inizializzazioni di avvio che possono essere richiesto da un'attività, ad esempio:

-  Creazione di viste
-  Inizializzazione di variabili
-  Associazione dati statici agli elenchi


`OnCreate` accetta un [Bundle](https://developer.xamarin.com/api/type/Android.OS.Bundle/) parametro, che è un dizionario per archiviare e passare le informazioni sullo stato e gli oggetti tra le attività se il pacchetto non è null, indica l'attività viene riavviato e deve ripristinare il relativo stato dal istanza precedente. Il codice seguente viene illustrato come recuperare i valori dal bundle di:

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

Una volta `OnCreate` è completata, verrà chiamata Android `OnStart`.

#### <a name="onstart"></a>OnStart

[OnStart](https://developer.xamarin.com/api/member/Android.App.Activity.OnStart/) viene sempre chiamato dal sistema quando `OnCreate` è terminato. Le attività possono eseguirne l'override di questo metodo se è necessario eseguire alcun diritto di attività specifiche prima di un'attività diventa visibile, ad esempio l'aggiornamento dei valori correnti delle viste all'interno dell'attività. Android chiamerà `OnResume` immediatamente dopo questo metodo.

#### <a name="onresume"></a>OnResume

Le chiamate di sistema [OnResume](https://developer.xamarin.com/api/member/Android.App.Activity.OnResume/) quando l'attività è pronto per iniziare a interagire con l'utente.
Le attività devono eseguire l'override di questo metodo per eseguire attività quali:

-  Aumentando frequenze (attività comuni nella creazione di giochi)
-  Animazioni iniziale
-  In attesa di aggiornamenti GPS
-  Visualizzati relativi avvisi o finestre di dialogo
-  Collegare gestori eventi esterni


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

`OnResume` è importante perché qualsiasi operazione che viene eseguita in `OnPause` deve essere annullato in `OnResume`perché è l'unico metodo di ciclo di vita che viene sempre eseguito dopo `OnPause` quando si attiva l'attività alla durata.

#### <a name="onpause"></a>OnPause

[OnPause](https://developer.xamarin.com/api/member/Android.App.Activity.OnPause/) viene chiamato quando il sistema sta per inserire l'attività in background o quando l'attività diventa parzialmente nascosto. Attività devono eseguire l'override di questo metodo se è necessario:

-   Eseguire il commit delle modifiche non salvate per dati persistenti

-   Eliminare o liberare gli altri oggetti di utilizzo di risorse

-   Uscita frequenze e le animazioni di sospensione

-   Annullare la registrazione di gestori eventi esterni o gestori di notifica (ad esempio quelle che sono collegati a un servizio). Questa operazione è necessaria per evitare perdite di memoria delle attività.

-   Analogamente, gli avvisi o finestre di dialogo, l'attività è visualizzato, devono essere eliminate con il `.Dismiss()` metodo.

Ad esempio, il frammento di codice seguente causerà il rilascio della fotocamera durante l'attività non è possibile utilizzarlo durante la sospensione di:

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

1.  `OnResume` verrà chiamato se l'attività è possibile restituire in primo piano.
1.  `OnStop` verrà chiamato se l'attività viene emesso in background.


#### <a name="onstop"></a>OnStop

[OnStop](https://developer.xamarin.com/api/member/Android.App.Activity.OnStop/) viene chiamato quando l'attività non è più visibile all'utente. Ciò si verifica quando si verifica una delle operazioni seguenti:

-  Una nuova attività viene avviata e copre di questa attività.
-  Un'attività esistente verrà presto portata in primo piano.
-  L'attività è in corso l'eliminazione.


`OnStop` non sempre è possibile chiamare in situazioni di memoria insufficiente, ad esempio quando Android è rimasto in scadenza per le risorse e l'attività non è correttamente in background. Per questo motivo, è consigliabile non fare affidamento su `OnStop` chiamato durante la preparazione di un'attività da eliminare. I metodi del ciclo di vita successivi che possono essere chiamati dopo che questo sarà `OnDestroy` se l'attività è deprecata o `OnRestart` se l'attività viene confermata l'interazione con l'utente.

#### <a name="ondestroy"></a>OnDestroy

[OnDestroy](https://developer.xamarin.com/api/member/Android.App.Activity.OnDestroy/) è il metodo finale che viene chiamato su un'istanza di attività prima che l'ha eliminato definitivamente e completamente rimossi dalla memoria. In alcune situazioni particolari Android potrebbe terminare il processo di applicazione che ospita l'attività, che produrrà `OnDestroy` non viene richiamato. La maggior parte delle attività non verrà implementata questo metodo perché la maggior parte pulire e arresto è stata effettuata nel `OnPause` e `OnStop` metodi. Il `OnDestroy` metodo in genere viene eseguito l'override per pulire prolungata che esegue le risorse che potrebbero perdite di risorse. Un esempio di questo potrebbe essere thread in background avviati nella `OnCreate`.

Non esistere alcun metodo del ciclo di vita viene chiamato dopo che l'attività è stato eliminato.

#### <a name="onrestart"></a>OnRestart

[OnRestart](https://developer.xamarin.com/api/member/Android.App.Activity.OnRestart/) viene chiamato dopo che l'attività è stata arrestata, prima viene riavviato. Quando l'utente preme il pulsante home mentre in un'attività nell'applicazione, si sarebbe un buon esempio di questo oggetto. In questo caso `OnPause` e quindi `OnStop` vengono chiamati i metodi e l'attività viene spostata in background, ma non viene eliminato. Se l'utente fosse per ripristinare l'applicazione usando Gestione attività o un'applicazione simile, Android chiamerà il `OnRestart` metodo dell'attività.

Non esistono regole generali per il tipo di logica deve essere implementato in `OnRestart`. In questo modo `OnStart` viene sempre richiamato indipendentemente dal fatto che l'attività viene creato o in fase di riavvio, pertanto qualsiasi risorsa necessaria per l'attività deve essere inizializzato nel `OnStart`, anziché `OnRestart`.

Metodo successivo ciclo di vita chiamato dopo `OnRestart` sarà `OnStart`.

### <a name="back-vs-home"></a>Eseguire il backup di Visual Studio. Home

Numero di dispositivi Android è due pulsanti distinti: un pulsante "Indietro" e un pulsante "Home". Un esempio di questo può essere visualizzato nella schermata seguente di Android è 4.0.3:

[![Pulsanti Indietro e Home](images/image4-sml.png)](images/image4.png#lightbox)

È una sottile differenza tra i due pulsanti, anche se sembrano hanno lo stesso effetto di inserimento di un'applicazione in background. Quando un utente fa clic sul pulsante Indietro, si indica Android che vengono eseguite con l'attività. Android determinerà l'eliminazione di attività. Al contrario, quando l'utente fa clic sul pulsante Home l'attività viene inserito soltanto in background &ndash; Android verrà non terminare l'attività.

<a name="Managing_State_Throughout_the_Lifecycle" />

## <a name="managing-state-throughout-the-lifecycle"></a>Gestione dello stato durante il ciclo di vita

Quando un'attività viene arrestata o eliminata definitivamente il sistema consente di salvare lo stato dell'attività per la riattivazione più avanti.
In questo stato salvato è detto lo stato dell'istanza. Android sono disponibili tre opzioni per archiviare lo stato dell'istanza durante il ciclo di vita di attività:

1. L'archiviazione di valori primitivi in un `Dictionary` noto come un [Bundle](https://developer.xamarin.com/api/type/Android.OS.Bundle/) che Android verrà utilizzato per salvare lo stato.

1. Creazione di una classe personalizzata che conterranno valori complessi, ad esempio le bitmap. Android utilizzerà la classe personalizzata per salvare lo stato.

1. Aggirare il ciclo di vita di modifica di configurazione e presupponendo che la completa responsabilità di gestione dello stato dell'attività.


Questa guida vengono illustrate le prime due opzioni.



### <a name="bundle-state"></a>Stato di aggregazione

L'opzione per salvare lo stato dell'istanza primaria è utilizzare un oggetto dizionario chiave-valore noto come un [Bundle](https://developer.xamarin.com/api/type/Android.OS.Bundle/).
Tenere presente che quando si crea un'attività di `OnCreate` viene passato un bundle come parametro, questo pacchetto può essere utilizzato per ripristinare lo stato dell'istanza. Non è consigliabile utilizzare un'aggregazione dei dati più complessi che non rapidamente o facilmente serializzare chiave/valore coppie (ad esempio le bitmap); e deve essere utilizzato per valori semplici come stringhe.

Un'attività fornisce metodi che consentono il salvataggio e il recupero dello stato dell'istanza nel Bundle:

-   [OnSaveInstanceState](https://developer.xamarin.com/api/member/Android.App.Activity.OnSaveInstanceState/p/Android.OS.Bundle/) &ndash; viene richiamato da Android quando l'attività è in corso l'eliminazione. Le attività possono implementare questo metodo se è necessaria rendere persistente qualsiasi degli elementi dello stato di chiave/valore.

-   [OnRestoreInstanceState](https://developer.xamarin.com/api/member/Android.App.Activity.OnRestoreInstanceState/p/Android.OS.Bundle/) &ndash; questo viene chiamato dopo il `OnCreate` metodo è terminato e fornisce un'altra possibilità per un'attività ripristinare lo stato dopo l'inizializzazione è completa.

Il diagramma seguente illustra come questi metodi vengono usati:

[![Diagramma di flusso stati bundle](images/image3-sml.png)](images/image3.png#lightbox)

#### <a name="onsaveinstancestate"></a>OnSaveInstanceState

[OnSaveInstanceState](https://developer.xamarin.com/api/member/Android.App.Activity.OnSaveInstanceState/p/Android.OS.Bundle/) verranno chiamati quando l'attività è stato arrestato. Riceverà un parametro di aggregazione che l'attività è possibile archiviare lo stato in. Quando un dispositivo di cui si verifichi una modifica della configurazione, è possibile utilizzare un'attività di `Bundle` oggetto passato per mantenere lo stato dell'attività eseguendo l'override `OnSaveInstanceState`. Si consideri il codice di esempio seguente:

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

Il codice precedente viene incrementato di un numero intero denominato `c` quando un pulsante denominato `incrementCounter` si fa clic, Visualizza il risultato in un `TextView` denominato `output`. Quando si verifica una modifica della configurazione, ad esempio, quando il dispositivo viene ruotato: il codice sopra riportato perderebbe il valore di `c` perché il `bundle` sarebbe `null`, come illustrato nella figura riportata di seguito:

[![Non viene visualizzato il valore precedente](images/07-sml.png)](images/07.png#lightbox)

Per mantenere il valore di `c` in questo esempio, l'attività è possibile eseguire l'override `OnSaveInstanceState`, salvare il valore nel bundle, come illustrato di seguito:

```csharp
protected override void OnSaveInstanceState (Bundle outState)
{
  outState.PutInt ("counter", c);
  base.OnSaveInstanceState (outState);
}
```

Ora quando il dispositivo viene ruotato in orientamento nuovo, il numero intero viene salvato nel bundle e verrà recuperato con la riga:

```csharp
c = bundle.GetInt ("counter", -1);
```

> [!NOTE]
> È importante sempre chiamata l'implementazione di base di `OnSaveInstanceState` in modo che può essere salvato anche lo stato della gerarchia di visualizzazione.



##### <a name="view-state"></a>Lo stato di visualizzazione

Si esegue l'override `OnSaveInstanceState` è un meccanismo appropriato per il salvataggio dei dati temporanei in un'attività durante le modifiche di orientamento, ad esempio il contatore nell'esempio precedente. Tuttavia, l'implementazione predefinita di `OnSaveInstanceState` si occuperà di salvataggio dei dati temporanei nell'interfaccia utente per ogni vista, purché ogni visualizzazione dispone di un ID assegnato. Se ad esempio, un'applicazione dispone di un `EditText` elemento definito in XML, come indicato di seguito:

```xml
<EditText android:id="@+id/myText"
  android:layout_width="fill_parent"
  android:layout_height="wrap_content"/>
```

Poiché il `EditText` controllo ha un `id` assegnati, quando l'utente immette alcuni dati e ruota il dispositivo, i dati vengono comunque visualizzati, come illustrato di seguito:

[![I dati vengono conservati in modalità orizzontale](images/08-sml.png)](images/08.png#lightbox)

#### <a name="onrestoreinstancestate"></a>OnRestoreInstanceState

[OnRestoreInstanceState](https://developer.xamarin.com/api/member/Android.App.Activity.OnRestoreInstanceState/p/Android.OS.Bundle/) verrà chiamato dopo `OnStart`. Fornisce un'attività la possibilità di ripristinare uno stato salvato in precedenza a un Bundle durante il precedente `OnSaveInstanceState`. Questo è il pacchetto stesso che viene fornito a `OnCreate`, tuttavia.

Il codice seguente viene illustrato come è possibile ripristinare lo stato nel `OnRestoreInstanceState`:

```csharp
protected override void OnRestoreInstanceState(Bundle savedState)
{
    base.OnRestoreSaveInstanceState(savedState);
    var myString = savedState.GetString("myString");
    var myBool = savedState.GetBoolean("myBool");
}
```

Questo metodo non esiste per prevedere una certa flessibilità intorno quando lo stato deve essere ripristinato. A volte è più opportuno attendere finché non vengono eseguite tutte le inizializzazioni prima di ripristinare lo stato dell'istanza. Una sottoclasse di un'attività esistente potrebbe inoltre decidere solo per il ripristino dello stato dell'istanza di determinati valori. In molti casi, non è necessario eseguire l'override `OnRestoreInstanceState`, poiché la maggior parte delle attività è possibile ripristinare lo stato tramite il bundle specificato per `OnCreate`.

Per un esempio di salvataggio stato utilizzando un `Bundle`, consultare il [procedura dettagliata: stato dell'attività di salvataggio](saving-state.md).


#### <a name="bundle-limitations"></a>Limitazioni di bundle

Sebbene `OnSaveInstanceState` rende facile salvare i dati temporanei, presenta alcune limitazioni:

-   Non viene chiamato in tutti i casi. Ad esempio, premere **Home** o **nuovamente** per uscire dall'installazione di un'attività non comporterà `OnSaveInstanceState` la chiamata.

-   Il pacchetto passato `OnSaveInstanceState` non è progettato per gli oggetti di grandi dimensioni, ad esempio immagini. Nel caso di oggetti di grandi dimensioni, il salvataggio dell'oggetto da [OnRetainNonConfigurationInstance](https://developer.xamarin.com/api/member/Android.App.Activity.OnRetainNonConfigurationInstance/) è preferibile, come descritto di seguito.

-   Dati salvati utilizzando il pacchetto viene serializzati, ciò può portare a ritardi.

Lo stato di raggruppamento è utile per i dati semplici che non utilizzano una quantità di memoria, mentre *dati dell'istanza di configurazione non* è utile per i dati più complesse o dati che è dispendioso recuperare, ad esempio da una chiamata al servizio web o un tipo complesso query di database. Dati dell'istanza di configurazione non salvati in un oggetto in base alle esigenze. La sezione successiva viene introdotta `OnRetainNonConfigurationInstance` allo scopo di preservare i tipi di dati più complessi tramite modifiche di configurazione.


### <a name="persisting-complex-data"></a>Rendere persistenti i dati complessi

Oltre a rendere persistenti i dati nel bundle, Android supporta inoltre il salvataggio dei dati eseguendo l'override [OnRetainNonConfigurationInstance](https://developer.xamarin.com/api/member/Android.App.Activity.OnRetainNonConfigurationInstance/) e restituisce un'istanza di un `Java.Lang.Object` che contiene i dati in modo permanente. Esistono due vantaggi principali dell'uso `OnRetainNonConfigurationInstance` di salvare lo stato:

-   L'oggetto restituito da `OnRetainNonConfigurationInstance` funziona bene con tipi di dati più grandi e complessi perché memoria mantiene questo oggetto.

-   Il `OnRetainNonConfigurationInstance` metodo viene chiamato su richiesta e solo quando necessario. Questo è più vantaggioso rispetto all'utilizzo di una cache manuale.

Utilizzando `OnRetainNonConfigurationInstance` è adatta agli scenari in cui è costoso recuperare i dati più volte, ad esempio chiamate al servizio web. Ad esempio, si consideri il codice seguente che cerca Twitter:

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

Questo codice consente di recuperare i risultati dal web formattati come JSON, li analizza e quindi presenta i risultati in un elenco, come illustrato nella schermata seguente:

[![Risultati visualizzati sullo schermo](images/06-sml.png)](images/06.png#lightbox)

Quando una modifica della configurazione si verifica, ad esempio, quando un dispositivo viene ruotato - il codice ripete il processo. Per riutilizzare i risultati originali recuperati e non provocano chiamate di rete ridondanti, inutili, è possibile utilizzare `OnRetainNonconfigurationInstance` per salvare i risultati, come illustrato di seguito:

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

Ora quando il dispositivo viene ruotato, i risultati originali vengono recuperati i `LastNonConfiguartionInstance` proprietà. In questo esempio, i risultati costituiti da un `string[]` contenente TWEET. Poiché `OnRetainNonConfigurationInstance` richiede che un `Java.Lang.Object` restituito, il `string[]` viene eseguito il wrapping in una classe che rappresenta una sottoclasse `Java.Lang.Object`, come illustrato di seguito:

```csharp
class TweetListWrapper : Java.Lang.Object
{
  public string[] Tweets { get; set; }
}
```

Ad esempio, il tentativo di utilizzare un `TextView` dell'oggetto restituito da `OnRetainNonConfigurationInstance` perderà l'attività, come illustrato nel codice seguente:

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

In questa sezione, è stato descritto come mantenere i dati di stato semplice con la `Bundle`, e mantenere i tipi di dati più complessi con `OnRetainNonConfigurationInstance`.

## <a name="summary"></a>Riepilogo

Il ciclo di vita di attività Android fornisce un framework potente per la gestione dello stato delle attività all'interno di un'applicazione, ma può essere difficile da comprendere e implementare. In questo capitolo è stato introdotto vari stati in cui un'attività potrebbe essere eseguita durante la sua durata, nonché i metodi del ciclo di vita che sono associati a tali Stati. Successivamente, fornito materiale sussidiario per il tipo di logica deve essere eseguito in ognuno di questi metodi.


## <a name="related-links"></a>Collegamenti correlati

- [Gestione della rotazione](~/android/app-fundamentals/handling-rotation.md)
- [Attività Android](https://developer.xamarin.com/api/type/Android.App.Activity/)
