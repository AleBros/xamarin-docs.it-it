---
title: Creazione di un frammento
ms.prod: xamarin
ms.assetid: F2997242-BC29-1440-7F1A-CFC447CD73FA
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/07/2018
ms.openlocfilehash: eae25dbfaf1125191a83b3cc4326abc19105f22f
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2018
ms.locfileid: "30770209"
---
# <a name="creating-a-fragment"></a>Creazione di un frammento

Per creare un frammento, è necessario ereditare una classe `Android.App.Fragment` e quindi eseguire l'override di `OnCreateView` metodo. `OnCreateView` verrà chiamato dall'host attività quando è necessario inserire il frammento sullo schermo e restituirà un `View`. Una tipica `OnCreateView` creerà `View` eccessi nella misurazione un file di layout e quindi collegarlo a un contenitore padre. Caratteristiche del contenitore sono importanti perché Android applicherà i parametri del layout dell'elemento padre per l'interfaccia utente del frammento. Questa condizione è illustrata nell'esempio che segue.

```csharp
public override View OnCreateView(LayoutInflater inflater, ViewGroup container, Bundle savedInstanceState)
{
    return inflater.Inflate(Resource.Layout.Example_Fragment, container, false);
}
```

Il codice sopra riportato aumenteranno la vista `Resource.Layout.Example_Fragment`e aggiungerlo come una visualizzazione figlio per il `ViewGroup` contenitore.


> [!NOTE]
> Le sottoclassi di frammento non devono avere valore predefinito è pubblico costruttore argomenti.

## <a name="adding-a-fragment-to-an-activity"></a>Aggiunta di un frammento a un'attività

Esistono due modi che può essere ospitato un frammento all'interno di un'attività:

-   **In modo dichiarativo** &ndash; frammenti possono essere utilizzati in modo dichiarativo all'interno `.axml` file di layout tramite la `<Fragment>` tag.

-   **A livello di programmazione** &ndash; frammenti possono anche essere creata un'istanza in modo dinamico tramite il `FragmentManager` API della classe.

Utilizzo a livello di codice tramite la `FragmentManager` classe verrà descritta più avanti in questa Guida.

### <a name="using-a-fragment-declaratively"></a>Utilizzo di un frammento di in modo dichiarativo

Aggiunta di un frammento all'interno del layout richiede l'utilizzo di `<fragment>` tag e quindi ne identifica il frammento, fornendo uno di `class` attributo o `android:name` attributo. Il frammento di codice seguente viene illustrato come utilizzare il `class` attributo per dichiarare un `fragment`:

```xml
<?xml version="1.0" encoding="utf-8"?>
<fragment class="com.xamarin.sample.fragments.TitlesFragment"
            android:id="@+id/titles_fragment"
            android:layout_width="fill_parent"
            android:layout_height="fill_parent" />
```

Questo frammento di codice successivo viene illustrato come dichiarare un `fragment` utilizzando il `android:name` attributo per identificare la classe frammento:

```xml
<?xml version="1.0" encoding="utf-8"?>
<fragment android:name="com.xamarin.sample.fragments.TitlesFragment"
            android:id="@+id/titles_fragment"
            android:layout_width="fill_parent"
            android:layout_height="fill_parent" />
```

Quando viene creato, l'attività Android verrà creare un'istanza di ogni frammento specificato nel file di layout e inserire la visualizzazione creata da `OnCreateView` anziché il `Fragment` elemento.
Frammenti che vengono aggiunti in modo dichiarativo a un'attività sono statici e rimarranno sull'attività fino a quando non viene eliminato; non è possibile sostituire in modo dinamico o rimuovere tale frammento tutta la durata dell'attività a cui è collegata.

Ogni frammento deve essere assegnato un identificatore univoco:

-  **Android: id** &ndash; come con altri elementi dell'interfaccia utente in un file di layout, non è un ID univoco.

-  **Android: tag** &ndash; questo attributo è una stringa univoca.

Se nessuno dei due metodi precedenti viene utilizzato, il frammento presupporrà l'ID della vista del contenitore. Nell'esempio seguente in cui nessuna delle due `android:id` né `android:tag` viene fornito, Android verrà assegnato l'ID `fragment_container` al frammento:

```xml
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
                android:id="+@id/fragment_container"
                android:orientation="horizontal"
                android:layout_width="match_parent"
                android:layout_height="match_parent">

        <fragment class="com.example.android.apis.app.TitlesFragment"
                android:layout_width="match_parent"
                android:layout_height="match_parent" />
</LinearLayout>
```

### <a name="package-name-case"></a>Pacchetto maiuscole/minuscole

Android non consente caratteri maiuscoli di nomi di pacchetto. verrà generata un'eccezione durante il tentativo di ingrandimento della visualizzazione, se il nome di un pacchetto contiene un carattere maiuscolo. Tuttavia, xamarin tollera meglio e sarà in grado di tollerare i caratteri maiuscoli nello spazio dei nomi.

Ad esempio, entrambi i frammenti di codice seguente funziona con xamarin. Tuttavia, si comporterà nel secondo frammento un `android.view.InflateException` deve essere generata da un'applicazione Android di basati su Java pura.

```xml
<fragment class="com.example.DetailsFragment" android:id="@+id/fragment_content" android:layout_width="match_parent" android:layout_height="match_parent" />
```

OR

```xml
<fragment class="Com.Example.DetailsFragment" android:id="@+id/fragment_content" android:layout_width="match_parent" android:layout_height="match_parent" />
```


## <a name="fragment-lifecycle"></a>Ciclo di vita del frammento

Frammenti hanno i propri ciclo di vita indipendente dei, ma ancora interessati da, il [ciclo di vita dell'attività hosting](~/android/app-fundamentals/activity-lifecycle/index.md).
Ad esempio, quando un'attività viene sospesa, tutti i relativi frammenti associati sono in pausa. Nel diagramma seguente illustra il ciclo di vita del frammento.

[![Diagramma di flusso che illustra il ciclo di vita di frammento](creating-a-fragment-images/fragment-lifecycle.png)](creating-a-fragment-images/fragment-lifecycle.png#lightbox)


### <a name="fragment-creation-lifecycle-methods"></a>Metodi di frammento per la creazione del ciclo di vita

Nell'elenco seguente mostra il flusso dei vari metodi di callback del ciclo di vita di un frammento, la creazione:

-   **`OnInflate()`** &ndash; Chiamato quando viene creato il frammento come parte di un layout di visualizzazione. Può essere chiamato immediatamente dopo il frammento viene creato in modo dichiarativo da un file di layout XML. Il frammento è associato a quello dell'attività non ancora, ma la **attività**, **Bundle**, e **AttributeSet** dalla visualizzazione gerarchia vengono passati come parametri. Questo metodo viene utilizzato meglio per l'analisi di **AttributeSet** e per salvare gli attributi che potrebbero essere utilizzate in un secondo momento dal frammento.

-   **`OnAttach()`** &ndash; Chiamato dopo che il frammento è associato all'attività. Questo è il primo metodo da eseguire quando il frammento è pronto per essere utilizzato. In generale, frammenti non devono implementare un costruttore o sostituire il costruttore predefinito. Tutti i componenti necessari per il frammento devono essere inizializzati in questo metodo.

-   **`OnCreate()`** &ndash; Chiamato dall'attività per creare il frammento. Quando questo metodo viene chiamato, la gerarchia della visualizzazione dell'attività di hosting potrebbe non essere completamente creata un'istanza, pertanto il frammento non deve basarsi su tutte le parti della gerarchia della visualizzazione dell'attività fino a quando non in un secondo momento nel ciclo di vita del frammento. Ad esempio, non utilizzare questo metodo per eseguire modifiche di entità o altre modifiche all'interfaccia utente dell'applicazione. Questa è la prima ora in cui il frammento può iniziare la raccolta dei dati che è necessario. Nel frammento di in esecuzione in thread dell'interfaccia utente a questo punto, pertanto evitare lunghe elaborazioni o elaborazione in un thread in background. Questo metodo può essere ignorato se **SetRetainInstance(true)** viene chiamato.
    Questa procedura alternativa verrà descritta in dettaglio più avanti.

-   **`OnCreateView()`** &ndash; Crea la visualizzazione per il frammento.
    Questo metodo viene chiamato una volta l'attività **OnCreate()** del metodo. A questo punto, è possibile interagire con la gerarchia della visualizzazione dell'attività. Questo metodo deve restituire la vista che verrà utilizzata dal frammento.

-   **`OnActivityCreated()`** &ndash; Chiamato dopo **OnCreate** è stata completata da parte dell'attività di hosting.
    Modifiche di entità finale per l'interfaccia utente deve essere eseguite in questo momento.

-   **`OnStart()`** &ndash; Chiamato dopo che l'attività che contiene è stata ripresa. In questo modo, il frammento è visibile all'utente. In molti casi, il frammento contiene codice che altrimenti sarebbe nel **metodo OnStart ()** metodo di un'attività.

-   **`OnResume()`** &ndash; Questo è l'ultimo metodo chiamato prima che l'utente può interagire con il frammento. Un esempio del tipo di codice che deve essere eseguito in questo metodo potrebbe essere attivazione di funzionalità di un dispositivo che l'utente può interagire, ad esempio la fotocamera che i servizi di posizione. Servizi come i seguenti possono causare un numero eccessivo della batteria, e un'applicazione deve ridurre al minimo l'utilizzo per mantenere la durata della batteria.


### <a name="fragment-destruction-lifecycle-methods"></a>Metodi del ciclo di vita di distruzione frammento

L'elenco seguente vengono illustrati i metodi di ciclo di vita che vengono chiamati come un frammento è in corso l'eliminazione:

-   **`OnPause()`** &ndash; L'utente non è più in grado di interagire con il frammento. Questa situazione si verifica perché un'altra operazione frammento la modifica di questo frammento, o l'host attività è stata sospesa. È possibile che l'attività di questo frammento di hosting potrebbe essere ancora visibile, vale a dire l'attività in stato attivo è parzialmente trasparente o non occupa l'intero schermo. Quando questo metodo diventa attivo, è la prima indicazione che l'utente lascia il frammento. Il frammento è consigliabile salvare le modifiche.

-   **`OnStop()`** &ndash; Il frammento non è più visibile. L'host attività potrebbe essere stato arrestato o un'operazione di frammento è modifica nell'attività. Questo callback viene utilizzata la stessa funzione **Activity.OnStop**.

-   **`OnDestroyView()`** &ndash; Questo metodo viene chiamato per pulire le risorse associate alla vista. Viene chiamato quando la visualizzazione associata al frammento è stata eliminata.

-   **`OnDestroy()`** &ndash; Questo metodo viene chiamato quando il frammento non è più in uso. È comunque associata all'attività, ma il frammento non è più funzionale. Questo metodo deve rilasciare le risorse che sono in uso dal frammento, ad esempio un [ **SurfaceView** ](https://developer.xamarin.com/api/type/Android.Views.SurfaceView/) che può essere usato per una fotocamera. Questo metodo può essere ignorato se **SetRetainInstance(true)** viene chiamato. Questa procedura alternativa verrà descritta in dettaglio più avanti.

-   **`OnDetach()`** &ndash; Questo metodo viene chiamato prima che il frammento non è più associato all'attività. La gerarchia della visualizzazione del frammento non esiste più, e tutte le risorse utilizzate dal frammento devono essere rilasciate a questo punto.


### <a name="using-setretaininstance"></a>Utilizzando SetRetainInstance

È possibile che un frammento specificare che non deve essere completamente eliminata se l'attività sta per essere ricreato. Il `Fragment` classe fornisce il metodo `SetRetainInstance` a questo scopo. Se `true` viene passato a questo metodo, verrà utilizzato quando l'attività viene riavviato, la stessa istanza del frammento. Se in questo caso, verranno richiamati tutti i metodi di callback, ad eccezione di `OnCreate` e `OnDestroy` i callback del ciclo di vita. Questo processo è illustrato nel diagramma del ciclo di vita illustrato in precedenza (per le linee punteggiate verde).


## <a name="fragment-state-management"></a>Gestione dello stato di frammento

Frammenti può salvare e ripristinare lo stato durante il ciclo di vita di frammento tramite un'istanza di un `Bundle`. Il Bundle consente un frammento di salvare i dati come coppie chiave/valore ed è utile per i dati semplici che non richiedono una quantità di memoria. Un frammento è possibile salvare lo stato con una chiamata a `OnSaveInstanceState`:

```csharp
public override void OnSaveInstanceState(Bundle outState)
{
    base.OnSaveInstanceState(outState);
    outState.PutInt("current_choice", _currentCheckPosition);
}
```

Quando viene creata una nuova istanza di un frammento, lo stato salvato nel `Bundle` verranno rese disponibili per la nuova istanza tramite il `OnCreate`, `OnCreateView`, e `OnActivityCreated` metodi della nuova istanza.
L'esempio seguente viene illustrato come recuperare il valore `current_choice` dal `Bundle`:

```csharp
public override void OnActivityCreated(Bundle savedInstanceState)
{
    base.OnActivityCreated(savedInstanceState);
    if (savedInstanceState != null)
    {
        _currentCheckPosition = savedInstanceState.GetInt("current_choice", 0);
    }
}
```

Si esegue l'override `OnSaveInstanceState` è un meccanismo appropriato per il salvataggio dei dati temporanei in un frammento le modifiche di orientamento, ad esempio il `current_choice` valore nell'esempio precedente. Tuttavia, l'implementazione predefinita di `OnSaveInstanceState` si occupa di salvare i dati temporanei nell'interfaccia utente per tutte le viste che è assegnato un ID. Esaminare, ad esempio, un'applicazione che ha un `EditText` elemento definito in XML, come indicato di seguito:

```xml
<EditText android:id="@+id/myText"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"/>
```

Poiché il `EditText` controllo ha un `id` assegnato, il frammento Salva automaticamente i dati all'interno del widget quando `OnSaveInstanceState` viene chiamato.


### <a name="bundle-limitations"></a>Limitazioni di bundle

Benché l'uso di `OnSaveInstanceState` rende facile salvare i dati temporanei, utilizzare questo metodo presenta alcune limitazioni:

-  Se il frammento non viene aggiunto allo stack indietro, quindi non verrà ripristinato lo stato quando l'utente preme il **nuovamente** pulsante.

-  Quando il pacchetto viene utilizzato per salvare i dati, i dati viene serializzati. Questo può causare ritardi di elaborazione.


## <a name="contributing-to-the-menu"></a>Che contribuiscono al Menu

Frammenti possono contribuire a elementi al menu della loro attività di hosting.
Un'attività gestisce innanzitutto le voci di menu. Se l'attività non dispone di un gestore, quindi l'evento verrà passata per il frammento, che verrà quindi gestire.

Per aggiungere elementi al menu dell'attività, è necessario un frammento di eseguire due operazioni.
In primo luogo, il frammento deve implementare il metodo `OnCreateOptionsMenu` e inserire le relative voci di menu, come illustrato nel codice seguente:

```csharp
public override void OnCreateOptionsMenu(IMenu menu, MenuInflater menuInflater)
{
    menuInflater.Inflate(Resource.Menu.menu_fragment_vehicle_list, menu);
    base.OnCreateOptionsMenu(menu, menuInflater);
}
```

Il menu nel frammento di codice precedente viene ingrandito dal codice XML seguente, si trova nel file `menu_fragment_vehicle_list.xml`:

```xml
<?xml version="1.0" encoding="utf-8"?>
<menu xmlns:android="http://schemas.android.com/apk/res/android">
  <item android:id="@+id/add_vehicle"
        android:icon="@drawable/ic_menu_add_data"
        android:title="@string/add_vehicle" />
</menu>
```

Successivamente, è necessario chiamare il frammento `SetHasOptionsMenu(true)`. La chiamata a questo metodo di notifica per Android che il frammento contiene voci di menu per contribuire al menu di opzioni. A meno che non viene effettuata la chiamata a questo metodo, le voci di menu per il frammento di non essere aggiunto al menu delle opzioni dell'attività. Questa operazione viene in genere eseguita nel metodo del ciclo di vita `OnCreate()`, come illustrato nel frammento di codice seguente:

```csharp
public override void OnCreate(Bundle savedState)
{
    base.OnCreate(savedState);
    SetHasOptionsMenu(true);
}
```

La schermata seguente è illustrata in questo menu:

[![Schermata di esempio di app personali trip la visualizzazione di voci di menu](creating-a-fragment-images/fragment-menu-example.png)](creating-a-fragment-images/fragment-menu-example.png#lightbox)
