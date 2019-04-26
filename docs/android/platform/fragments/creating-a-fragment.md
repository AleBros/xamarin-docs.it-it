---
title: Creazione di un frammento
ms.prod: xamarin
ms.assetid: F2997242-BC29-1440-7F1A-CFC447CD73FA
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/07/2018
ms.openlocfilehash: 339de4930242e35c40b034af2ce6ba47fe1543af
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61023673"
---
# <a name="creating-a-fragment"></a>Creazione di un frammento

Per creare un frammento, è necessario ereditare una classe `Android.App.Fragment` e quindi eseguire l'override di `OnCreateView` (metodo). `OnCreateView` verrà chiamato dall'attività di hosting quando è necessario inserire il frammento sullo schermo e restituirà un `View`. Una tipica `OnCreateView` creerà `View` ciò fa aumentare erroneamente un file di layout e quindi collegarlo a un contenitore padre. Caratteristiche del contenitore sono importanti perché Android applicherà i parametri del layout dell'elemento padre per l'interfaccia utente del frammento. Questa condizione è illustrata nell'esempio che segue.

```csharp
public override View OnCreateView(LayoutInflater inflater, ViewGroup container, Bundle savedInstanceState)
{
    return inflater.Inflate(Resource.Layout.Example_Fragment, container, false);
}
```

Il codice sopra riportato aumenteranno la vista `Resource.Layout.Example_Fragment`e aggiungerla come una visualizzazione figlio per il `ViewGroup` contenitore.


> [!NOTE]
> Le sottoclassi di frammento non devono avere un pubblico predefinito costruttore argomenti.

## <a name="adding-a-fragment-to-an-activity"></a>Aggiunta di un frammento a un'attività

Esistono due modi che un frammento può essere ospitato all'interno di un'attività:

-   **In modo dichiarativo** &ndash; è possibile usare in modo dichiarativo ai frammenti `.axml` i file di layout tramite la `<Fragment>` tag.

-   **A livello di programmazione** &ndash; frammenti sono anche possibile creare istanze in modo dinamico usando il `FragmentManager` API della classe.

Utilizzo a livello di programmazione tramite le `FragmentManager` classe verrà descritta più avanti in questa Guida.

### <a name="using-a-fragment-declaratively"></a>Utilizza in modo dichiarativo un frammento

Aggiunta di un frammento all'interno del layout richiede l'uso di `<fragment>` tag e quindi identificando il frammento, fornendo uno il `class` attributo o il `android:name` attributo. Il frammento seguente illustra come usare il `class` attributi per dichiarare un `fragment`:

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

Quando viene creato, l'attività Android verranno creare un'istanza di ogni frammento specificato nel file di layout e inserire la vista che viene creata da `OnCreateView` anziché il `Fragment` elemento.
I frammenti che vengono aggiunti in modo dichiarativo a un'attività sono statici e rimarranno sull'attività fino a quando non viene eliminato; non è possibile sostituire o rimuovere tale frammento nel corso della durata dell'attività a cui è collegata in modo dinamico.

Ogni frammento deve essere assegnato un identificatore univoco:

-  **Android: id** &ndash; poiché gli altri elementi dell'interfaccia utente in un file di layout, si tratta di un ID univoco.

-  **Android: tag** &ndash; questo attributo è una stringa univoca.

Se nessuno dei due metodi precedenti viene utilizzato, il frammento presupporrà l'ID della visualizzazione del contenitore. Nell'esempio seguente in cui nessuno dei due `android:id` né `android:tag` è specificato, Android assegnerà l'ID `fragment_container` al frammento:

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

Android non consente caratteri maiuscoli di nomi di pacchetto. verrà generata un'eccezione durante il tentativo di aumento della visualizzazione, se il nome del pacchetto contiene un carattere maiuscolo. Tuttavia, xamarin. Android è più tollerante e consentiranno di tollerare i caratteri maiuscoli nello spazio dei nomi.

Ad esempio, entrambi i frammenti di codice seguente funzioneranno con xamarin. Android. Tuttavia, il secondo frammento causerà un `android.view.InflateException` deve essere generata da un'applicazione di Android pura basate su Java.

```xml
<fragment class="com.example.DetailsFragment" android:id="@+id/fragment_content" android:layout_width="match_parent" android:layout_height="match_parent" />
```

OR

```xml
<fragment class="Com.Example.DetailsFragment" android:id="@+id/fragment_content" android:layout_width="match_parent" android:layout_height="match_parent" />
```


## <a name="fragment-lifecycle"></a>Ciclo di vita del frammento

I frammenti sono proprio ciclo di vita indipendente dei, ma ancora interessati, il [ciclo di vita delle attività di hosting](~/android/app-fundamentals/activity-lifecycle/index.md).
Ad esempio, quando un'attività viene sospesa, tutti relativi frammenti associati sono in pausa. Il diagramma seguente illustra il ciclo di vita del frammento.

[![Diagramma di flusso che illustra il ciclo di vita di frammento](creating-a-fragment-images/fragment-lifecycle.png)](creating-a-fragment-images/fragment-lifecycle.png#lightbox)


### <a name="fragment-creation-lifecycle-methods"></a>Metodi del ciclo di vita di creazione di frammenti

L'elenco seguente illustra il flusso dei vari callback del ciclo di vita di un frammento come la creazione:

-   **`OnInflate()`** &ndash; Chiamato quando viene creato il frammento come parte di un layout di visualizzazione. Ciò può essere chiamato immediatamente dopo aver creato in modo dichiarativo il frammento da un file di layout XML. Il frammento non è associato con la propria attività ancora, ma il **attività**, **Bundle**, e **AttributeSet** dalla visualizzazione gerarchia vengono passati come parametri. Questo metodo viene utilizzato meglio per l'analisi il **AttributeSet** e per salvare gli attributi che potrebbe essere utilizzato in un secondo momento dal frammento.

-   **`OnAttach()`** &ndash; Chiamato dopo che il frammento è associato all'attività. Questo è il primo metodo da eseguire quando il frammento è pronto per essere usato. In generale, i frammenti devono non implementa alcun costruttore o sostituire il costruttore predefinito. Tutti i componenti necessari per il frammento devono essere inizializzati in questo metodo.

-   **`OnCreate()`** &ndash; Chiamato dall'attività per creare il frammento. Quando questo metodo viene chiamato, la gerarchia di visualizzazione dell'attività di hosting potrebbe non essere completamente creata un'istanza, in modo che il frammento non deve basarsi su tutte le parti della gerarchia di visualizzazione dell'attività fino a quando non in un secondo momento nel ciclo di vita del frammento. Ad esempio, non utilizzare questo metodo per eseguire modifiche di lieve o altre modifiche all'interfaccia utente dell'applicazione. Si tratta ora la prima volta in corrispondenza del quale il frammento può iniziare a raccogliere i dati necessari. Il frammento viene eseguito in thread dell'interfaccia utente a questo punto, pertanto, evitare lunghe elaborazioni o eseguire l'elaborazione su un thread in background. Questo metodo può essere ignorato se **SetRetainInstance(true)** viene chiamato.
    Questa alternativa verrà descritte in dettaglio più avanti.

-   **`OnCreateView()`** &ndash; Crea la visualizzazione per il frammento.
    Questo metodo viene chiamato una volta l'attività **OnCreate()** metodo è stato completato. A questo punto, è sicuro interagire con la gerarchia di visualizzazione dell'attività. Questo metodo deve restituire la visualizzazione che verrà usata dal frammento.

-   **`OnActivityCreated()`** &ndash; Chiamato dopo **OnCreate** è stata completata dall'attività di hosting.
    Modifiche all'interfaccia utente devono essere eseguite in questo momento.

-   **`OnStart()`** &ndash; Chiamato dopo che l'attività che contiene è stata ripresa. In questo modo, il frammento è visibile all'utente. In molti casi, il frammento contiene codice che verrebbero altrimenti il **OnStart ()** metodo di un'attività.

-   **`OnResume()`** &ndash; Questo è l'ultimo metodo chiamato prima che l'utente può interagire con il frammento. Un esempio del tipo di codice che deve essere eseguito in questo metodo potrebbe essere abilitazione delle funzionalità di un dispositivo che l'utente può interagire, ad esempio la fotocamera che i servizi di posizione. Servizi come i seguenti possono causare un numero eccessivo della batteria, tuttavia, e un'applicazione deve ridurre al minimo l'utilizzo per mantenere la durata della batteria.


### <a name="fragment-destruction-lifecycle-methods"></a>Metodi del ciclo di vita di distruzione di $ frammento

Il successivo elenco illustra i metodi del ciclo di vita che vengono definiti come un frammento viene eliminata definitivamente:

-   **`OnPause()`** &ndash; L'utente non è più in grado di interagire con il frammento. Questa situazione si verifica perché un'altra operazione frammento apporta modifiche a questo frammento, o l'attività di hosting è in pausa. È possibile che l'attività di questo frammento di hosting potrebbe essere ancora visibile, vale a dire, l'attività attiva è parzialmente trasparente o non occupa l'intero schermo. Quando questo metodo diventa attivo, è la prima indicazione che l'utente sta abbandonando il frammento. Il frammento deve salvare le modifiche.

-   **`OnStop()`** &ndash; Il frammento non è più visibile. L'host attività potrebbe essere stato arrestato o un'operazione di frammento è modificarla nell'attività. Questo callback viene usato lo stesso scopo **Activity.OnStop**.

-   **`OnDestroyView()`** &ndash; Questo metodo viene chiamato per pulire le risorse associate alla vista. Viene chiamato quando la visualizzazione associata al frammento è stata eliminata.

-   **`OnDestroy()`** &ndash; Questo metodo viene chiamato quando il frammento non è più in uso. È comunque associata all'attività, ma il frammento non è più funzionale. Questo metodo deve rilasciare le risorse che sono in uso dal frammento, ad esempio un [ **SurfaceView** ](https://developer.xamarin.com/api/type/Android.Views.SurfaceView/) che potrebbero essere usate per una fotocamera. Questo metodo può essere ignorato se **SetRetainInstance(true)** viene chiamato. Questa alternativa verrà descritte in dettaglio più avanti.

-   **`OnDetach()`** &ndash; Questo metodo viene chiamato appena prima che il frammento non è più associato all'attività. La gerarchia di visualizzazione del frammento non esiste più, e tutte le risorse usate dal frammento devono essere rilasciate a questo punto.


### <a name="using-setretaininstance"></a>Usando SetRetainInstance

È possibile che un frammento specificare che non deve essere completamente eliminata se l'attività è stata ricreata. Il `Fragment` classe fornisce il metodo `SetRetainInstance` per questo scopo. Se `true` viene passata a questo metodo, verrà utilizzato quando l'attività viene riavviato, la stessa istanza del frammento. Se in questo caso, quindi tutti i metodi di callback verranno richiamati, ad eccezione di `OnCreate` e `OnDestroy` i callback del ciclo di vita. Questo processo è illustrato nel diagramma precedente del ciclo di vita (per le linee punteggiate verde).


## <a name="fragment-state-management"></a>Gestione dello stato di frammento

Frammenti maggio salvare e ripristinare lo stato durante il ciclo di vita di frammento con un'istanza di un `Bundle`. Bundle consente a un frammento da salvare i dati come coppie chiave/valore e la gestione dei dati semplici che non richiedono una quantità eccessiva di memoria. Un frammento possibile salvarne lo stato con una chiamata a `OnSaveInstanceState`:

```csharp
public override void OnSaveInstanceState(Bundle outState)
{
    base.OnSaveInstanceState(outState);
    outState.PutInt("current_choice", _currentCheckPosition);
}
```

Quando viene creata una nuova istanza di un frammento, lo stato salvato nel `Bundle` diventeranno disponibili per la nuova istanza tramite il `OnCreate`, `OnCreateView`, e `OnActivityCreated` metodi della nuova istanza.
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

Si esegue l'override `OnSaveInstanceState` è un meccanismo appropriato per il salvataggio dei dati temporanei in un frammento tra le modifiche apportate orientamento, ad esempio il `current_choice` valore nell'esempio precedente. Tuttavia, l'implementazione predefinita di `OnSaveInstanceState` si occupa di salvare i dati temporanei nell'interfaccia utente per ogni vista in cui è assegnato un ID. Ad esempio, esaminata un'applicazione che ha un `EditText` elemento definito in XML, come indicato di seguito:

```xml
<EditText android:id="@+id/myText"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"/>
```

Poiché il `EditText` controllo ha un `id` assegnato, il frammento Salva automaticamente i dati all'interno del widget quando `OnSaveInstanceState` viene chiamato.


### <a name="bundle-limitations"></a>Limitazioni di aggregazione

Sebbene l'utilizzo `OnSaveInstanceState` rende facile Salva i dati temporanei, utilizzare questo metodo presenta alcune limitazioni:

-  Se il frammento non viene aggiunta allo stack indietro, quindi il relativo stato non verrà ripristinato quando l'utente preme il **nuovamente** pulsante.

-  Quando il pacchetto viene usato per salvare i dati, tali dati viene serializzati. Questo può causare ritardi di elaborazione.


## <a name="contributing-to-the-menu"></a>Aggiunta come contributo al Menu

Frammenti possono contribuire voci al menu della relativa ' attività hosting.
Un'attività gestisce prima di tutto le voci di menu. Se l'attività non ha un gestore, quindi l'evento verrà passato per il frammento, che verrà quindi gestirlo.

Per aggiungere voci al menu dell'attività, un frammento è necessario eseguire due operazioni.
In primo luogo, il frammento necessario implementare il metodo `OnCreateOptionsMenu` e inserire i relativi elementi nel menu, come illustrato nel codice seguente:

```csharp
public override void OnCreateOptionsMenu(IMenu menu, MenuInflater menuInflater)
{
    menuInflater.Inflate(Resource.Menu.menu_fragment_vehicle_list, menu);
    base.OnCreateOptionsMenu(menu, menuInflater);
}
```

Nel menu nel frammento di codice precedente viene sottoposto a inflating dal codice XML seguente, che si trova nel file `menu_fragment_vehicle_list.xml`:

```xml
<?xml version="1.0" encoding="utf-8"?>
<menu xmlns:android="http://schemas.android.com/apk/res/android">
  <item android:id="@+id/add_vehicle"
        android:icon="@drawable/ic_menu_add_data"
        android:title="@string/add_vehicle" />
</menu>
```

Successivamente, è necessario chiamare il frammento `SetHasOptionsMenu(true)`. La chiamata a questo metodo notifica al Android che il frammento ha voci di menu per contribuire al menu di opzione. A meno che non viene effettuata la chiamata a questo metodo, non le voci di menu per il frammento verranno aggiunto al menu delle opzioni dell'attività. Ciò avviene in genere nel metodo del ciclo di vita `OnCreate()`, come illustrato nel frammento di codice seguente:

```csharp
public override void OnCreate(Bundle savedState)
{
    base.OnCreate(savedState);
    SetHasOptionsMenu(true);
}
```

La schermata seguente mostra come potrebbe apparire questo menu:

[![Screenshot di esempio dell'app personali trip la visualizzazione di voci di menu](creating-a-fragment-images/fragment-menu-example.png)](creating-a-fragment-images/fragment-menu-example.png#lightbox)
