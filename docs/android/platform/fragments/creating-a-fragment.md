---
title: Creazione di un frammentoCreating A Fragment
ms.prod: xamarin
ms.assetid: F2997242-BC29-1440-7F1A-CFC447CD73FA
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/07/2018
ms.openlocfilehash: 0e8d3748c7ddd337cf2f27f5b272b208e79d503a
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2020
ms.locfileid: "73027508"
---
# <a name="creating-a-fragment"></a>Creazione di un frammentoCreating A Fragment

Per creare un frammento, `Android.App.Fragment` una classe `OnCreateView` deve ereditare da e quindi eseguire l'override del metodo. `OnCreateView`verrà chiamato dall'attività di hosting quando è il momento di inserire `View`il frammento sullo schermo e restituirà un file . Un `OnCreateView` tipico creerà questo `View` gonfiando un file di layout e quindi collegandolo a un contenitore padre. Le caratteristiche del contenitore sono importanti in quanto Android applicherà i parametri di layout dell'elemento padre all'interfaccia utente del frammento. L'esempio seguente illustra questi concetti.

```csharp
public override View OnCreateView(LayoutInflater inflater, ViewGroup container, Bundle savedInstanceState)
{
    return inflater.Inflate(Resource.Layout.Example_Fragment, container, false);
}
```

Il codice precedente gonfierà `Resource.Layout.Example_Fragment`la visualizzazione e la aggiungerà come visualizzazione figlio al `ViewGroup` contenitore.

> [!NOTE]
> Le sottoclassi di frammento devono avere un costruttore di argomento predefinito pubblico.

## <a name="adding-a-fragment-to-an-activity"></a>Aggiunta di un frammento a un'attivitàAdding a Fragment to an Activity

Esistono due modi in cui un frammento può essere ospitato all'interno di un'attività:There are two ways that a Fragment may be hosted inside an Activity:

- Frammenti in modo **dichiarativo** possono `.axml` essere utilizzati `<Fragment>` in modo dichiarativo all'interno di file di layout utilizzando il tag. &ndash;

- **È** &ndash; inoltre possibile creare un'istanza dinamica `FragmentManager` di frammenti a livello di codice usando l'API della classe.

L'utilizzo a `FragmentManager` livello di codice tramite la classe verrà illustrato più avanti in questa guida.

### <a name="using-a-fragment-declaratively"></a>Utilizzo di un frammento in modo dichiarativoUsing a Fragment Declaratively

L'aggiunta di un frammento all'interno del layout richiede l'utilizzo del `<fragment>` tag e quindi l'identificazione del frammento specificando l'attributo `class` o l'attributo `android:name` . Nel frammento di codice `class` seguente viene `fragment`illustrato come utilizzare l'attributo per dichiarare un:

```xml
<?xml version="1.0" encoding="utf-8"?>
<fragment class="com.xamarin.sample.fragments.TitlesFragment"
            android:id="@+id/titles_fragment"
            android:layout_width="fill_parent"
            android:layout_height="fill_parent" />
```

Nel frammento successivo viene `fragment` illustrato `android:name` come dichiarare un utilizzando l'attributo per identificare la classe Fragment :

```xml
<?xml version="1.0" encoding="utf-8"?>
<fragment android:name="com.xamarin.sample.fragments.TitlesFragment"
            android:id="@+id/titles_fragment"
            android:layout_width="fill_parent"
            android:layout_height="fill_parent" />
```

Quando l'attività viene creata, Android verrà creata un'istanza di ogni `OnCreateView` frammento specificato `Fragment` nel file di layout e inserire la visualizzazione che viene creata da al posto dell'elemento.
I frammenti aggiunti in modo dichiarativo a un'attività sono statici e rimarranno nell'attività fino a quando non vengono eliminati; non è possibile sostituire o rimuovere dinamicamente tale frammento durante la durata dell'attività a cui è collegato.

A ogni frammento deve essere assegnato un identificatore univoco:Each Fragment must be assigned a unique identifier:

- **android:id** &ndash; Come con altri elementi dell'interfaccia utente in un file di layout, si tratta di un ID univoco.

- **android:tag** &ndash; Questo attributo è una stringa univoca.

Se nessuno dei due metodi precedenti viene utilizzato, il frammento assumerà l'ID della visualizzazione contenitore. Nell'esempio seguente `android:id` in `android:tag` cui non viene fornito `fragment_container` né, Android assegnerà l'ID al frammento:In the following example where nor is provided, Android will assign the ID to the Fragment:

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

### <a name="package-name-case"></a>Caso nome pacchetto

Android non consente caratteri maiuscoli nei nomi dei pacchetti; genererà un'eccezione quando si tenta di gonfiare la visualizzazione se il nome di un pacchetto contiene un carattere maiuscolo. Tuttavia, Xamarin.Android è più indulgente e tollererà i caratteri maiuscoli nello spazio dei nomi.

Ad esempio, entrambi i frammenti seguenti funzioneranno con Xamarin.Android.For example, both of the following snippets will work with Xamarin.Android. Tuttavia, il secondo `android.view.InflateException` frammento causerà un essere generato da un'applicazione Android basata su Java pura.

```xml
<fragment class="com.example.DetailsFragment" android:id="@+id/fragment_content" android:layout_width="match_parent" android:layout_height="match_parent" />
```

OR

```xml
<fragment class="Com.Example.DetailsFragment" android:id="@+id/fragment_content" android:layout_width="match_parent" android:layout_height="match_parent" />
```

## <a name="fragment-lifecycle"></a>Ciclo di vita dei frammentiFragment Lifecycle

I frammenti hanno un proprio ciclo di vita che è in qualche modo indipendente, ma comunque influenzato dal [ciclo di vita dell'attività di hosting.](~/android/app-fundamentals/activity-lifecycle/index.md)
Ad esempio, quando un'attività viene sospesa, tutti i frammenti associati vengono messi in pausa. Il diagramma seguente illustra il ciclo di vita del frammento.

[![Diagramma di flusso che illustra il ciclo di vita dei frammenti](creating-a-fragment-images/fragment-lifecycle.png)](creating-a-fragment-images/fragment-lifecycle.png#lightbox)

### <a name="fragment-creation-lifecycle-methods"></a>Metodi del ciclo di vita della creazione di frammentiFragment Creation Lifecycle Methods

L'elenco seguente mostra il flusso dei vari callback nel ciclo di vita di un frammento durante la creazione:The list below shows the flow of the various callbacks in the lifecycle of a Fragment as it is being created:

- **`OnInflate()`**&ndash; Chiamato quando il frammento viene creato come parte di un layout di visualizzazione. Questo può essere chiamato immediatamente dopo il frammento viene creato in modo dichiarativo da un file di layout XML. Il frammento non è ancora associato alla relativa attività, ma i valori **Activity**, **Bundle**e **AttributeSet** della gerarchia di visualizzazione vengono passati come parametri. Questo metodo è ideale per l'analisi di **AttributeSet** e per il salvataggio degli attributi che potrebbero essere utilizzati in un secondo momento dal frammento.

- **`OnAttach()`**&ndash; Chiamato dopo che il frammento è associato all'attività. Questo è il primo metodo da eseguire quando il frammento è pronto per essere utilizzato. In generale, i frammenti non devono implementare un costruttore o eseguire l'override del costruttore predefinito. Tutti i componenti necessari per il frammento devono essere inizializzati in questo metodo.

- **`OnCreate()`**&ndash; Chiamato dall'attività per creare il frammento. Quando questo metodo viene chiamato, la gerarchia di visualizzazione dell'attività di hosting potrebbe non essere completamente creata un'istanza, pertanto il frammento non deve basarsi su parti della gerarchia di visualizzazione dell'attività fino a un'altra nel ciclo di vita del frammento. Ad esempio, non utilizzare questo metodo per eseguire modifiche o modifiche all'interfaccia utente dell'applicazione. Questo è il primo momento in cui il frammento può iniziare a raccogliere i dati necessari. Il frammento è in esecuzione nel thread dell'interfaccia utente a questo punto, quindi evitare qualsiasi elaborazione lunga o eseguire tale elaborazione in un thread in background. Questo metodo può essere ignorato se **SetRetainInstance(true)** viene chiamato.
    Questa alternativa verrà descritta in dettaglio di seguito.

- **`OnCreateView()`**&ndash; Crea la visualizzazione per il frammento.
    Questo metodo viene chiamato una volta completato il metodo **OnCreate()** dell'attività. A questo punto, è possibile interagire con la gerarchia di visualizzazione dell'attività. Questo metodo deve restituire la visualizzazione che verrà utilizzata dal frammento.

- **`OnActivityCreated()`**&ndash; Chiamato dopo **Activity.OnCreate** è stato completato dall'attività di hosting.
    Le modifiche finali all'interfaccia utente devono essere eseguite in questo momento.

- **`OnStart()`**&ndash; Chiamato dopo la ripresa dell'attività contenitore. In questo modo il frammento visibile all'utente. In molti casi, il frammento conterrà codice che altrimenti sarebbe nel metodo **OnStart()** di un'attività.

- **`OnResume()`**&ndash; Questo è l'ultimo metodo chiamato prima che l'utente possa interagire con il frammento. Un esempio del tipo di codice che deve essere eseguito in questo metodo potrebbe essere l'abilitazione delle funzionalità di un dispositivo con cui l'utente può interagire, ad esempio la fotocamera che i servizi di posizione. Servizi come questi possono causare un eccessivo consumo della batteria, però, e un'applicazione dovrebbe ridurre al minimo il loro uso per preservare la durata della batteria.

### <a name="fragment-destruction-lifecycle-methods"></a>Metodi del ciclo di vita di distruzione dei frammentiFragment Destruction Lifecycle

Nell'elenco seguente vengono illustrati i metodi del ciclo di vita chiamati come frammento viene eliminato:The next list explains the lifecycle methods that are called as a Fragment is being destroyed:

- **`OnPause()`**&ndash; L'utente non è più in grado di interagire con il frammento. Questa situazione si verifica perché un'altra operazione di frammento sta modificando questo frammento o l'attività di hosting è sospesa. È possibile che l'attività che ospita questo frammento sia ancora visibile, ovvero che l'attività attiva sia parzialmente trasparente o non occupi lo schermo intero. Quando questo metodo diventa attivo, è la prima indicazione che l'utente sta lasciando il frammento. Il frammento deve salvare le modifiche.

- **`OnStop()`**&ndash; Il frammento non è più visibile. L'attività host può essere arrestata o un'operazione di frammento lo sta modificando nell'attività. Questo callback ha lo stesso scopo di **Activity.OnStop**.

- **`OnDestroyView()`**&ndash; Questo metodo viene chiamato per pulire le risorse associate alla visualizzazione. Viene chiamato quando la visualizzazione associata al frammento è stata eliminata.

- **`OnDestroy()`**&ndash; Questo metodo viene chiamato quando il frammento non è più in uso. È ancora associato all'attività, ma il frammento non è più funzionale. Questo metodo deve rilasciare tutte le risorse utilizzate dal frammento, ad esempio un [**SurfaceView**](xref:Android.Views.SurfaceView) che potrebbe essere utilizzato per una fotocamera. Questo metodo può essere ignorato se **SetRetainInstance(true)** viene chiamato. Questa alternativa verrà descritta in dettaglio di seguito.

- **`OnDetach()`**&ndash; Questo metodo viene chiamato appena prima che il frammento non è più associato il Activity. La gerarchia di visualizzazione del frammento non esiste più e tutte le risorse utilizzate dal frammento devono essere rilasciate a questo punto.

### <a name="using-setretaininstance"></a>Utilizzo di SetRetainInstanceUsing SetRetainInstance

È possibile che un frammento specifichi che non deve essere completamente eliminato se l'attività viene ricreata. La `Fragment` classe fornisce `SetRetainInstance` il metodo a questo scopo. Se `true` viene passato a questo metodo, quando l'attività viene riavviata, verrà utilizzata la stessa istanza del frammento. In questo caso, verranno richiamati tutti `OnCreate` `OnDestroy` i metodi di callback, ad eccezione dei callback e del ciclo di vita. Questo processo è illustrato nel diagramma del ciclo di vita illustrato in precedenza (dalle linee tratteggiate verdi).

## <a name="fragment-state-management"></a>Gestione dello stato dei frammentiFragment State Management

I frammenti possono salvare e ripristinare il relativo `Bundle`stato durante il ciclo di vita del frammento utilizzando un'istanza di un oggetto . Il pacchetto consente a un frammento di salvare i dati come coppie chiave/valore ed è utile per dati semplici che non richiedono molta memoria. Un frammento può salvare il `OnSaveInstanceState`proprio stato con una chiamata a :

```csharp
public override void OnSaveInstanceState(Bundle outState)
{
    base.OnSaveInstanceState(outState);
    outState.PutInt("current_choice", _currentCheckPosition);
}
```

Quando viene creata una nuova istanza di un `Bundle` frammento, lo stato `OnCreate`salvato `OnCreateView`in `OnActivityCreated` diventerà disponibile per la nuova istanza tramite i metodi , e della nuova istanza.
Nell'esempio seguente viene illustrato `current_choice` come `Bundle`recuperare il valore dal :

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

L'override `OnSaveInstanceState` è un meccanismo appropriato per salvare i dati temporanei `current_choice` in un frammento tra le modifiche di orientamento, ad esempio il valore nell'esempio precedente. Tuttavia, l'implementazione predefinita di `OnSaveInstanceState` si occupa del salvataggio dei dati temporanei nell'interfaccia utente per ogni visualizzazione a cui è assegnato un ID. Ad esempio, esaminare un'applicazione che dispone di un elemento definito in XML come segue:For example, look at an application that has an `EditText` element defined in XML as follows:

```xml
<EditText android:id="@+id/myText"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"/>
```

Poiché `EditText` al `id` controllo è assegnato un controllo, il `OnSaveInstanceState` frammento salva automaticamente i dati nel widget quando viene chiamato.

### <a name="bundle-limitations"></a>Limitazioni del pacchetto

Anche `OnSaveInstanceState` se l'utilizzo semplifica il salvataggio dei dati temporanei, l'uso di questo metodo presenta alcune limitazioni:Although using makes it easy to save transient data, use of this method has some limitations:

- Se il frammento non viene aggiunto allo stack Indietro, il relativo stato non verrà ripristinato quando l'utente preme il pulsante **Indietro.**

- Quando il Bundle viene utilizzato per salvare i dati, tali dati vengono serializzati. Ciò può causare ritardi nell'elaborazione.

## <a name="contributing-to-the-menu"></a>Contribuire al menu

I frammenti possono contribuire elementi al menu dell'attività di hosting.
Un'attività gestisce prima le voci di menu. Se l'attività non dispone di un gestore, l'evento verrà passato al frammento, che lo gestirà.

Per aggiungere elementi al menu dell'attività, un frammento deve eseguire due operazioni.
In primo luogo, il `OnCreateOptionsMenu` frammento deve implementare il metodo e inserire gli elementi nel menu, come illustrato nel codice seguente:First, the Fragment must implement the method and place its items into the menu, as shown in the following code:

```csharp
public override void OnCreateOptionsMenu(IMenu menu, MenuInflater menuInflater)
{
    menuInflater.Inflate(Resource.Menu.menu_fragment_vehicle_list, menu);
    base.OnCreateOptionsMenu(menu, menuInflater);
}
```

Il menu nel frammento di codice precedente viene gonfiato `menu_fragment_vehicle_list.xml`dal codice XML seguente, che si trova nel file :

```xml
<?xml version="1.0" encoding="utf-8"?>
<menu xmlns:android="http://schemas.android.com/apk/res/android">
  <item android:id="@+id/add_vehicle"
        android:icon="@drawable/ic_menu_add_data"
        android:title="@string/add_vehicle" />
</menu>
```

Successivamente, il frammento deve chiamare `SetHasOptionsMenu(true)`. La chiamata a questo metodo annuncia ad Android che il frammento dispone di voci di menu per contribuire al menu delle opzioni. A meno che non venga effettuata la chiamata a questo metodo, le voci di menu per il frammento non verranno aggiunte al menu delle opzioni dell'attività. Questa operazione viene in `OnCreate()`genere eseguita nel metodo del ciclo di vita , come illustrato nel frammento di codice successivo:This is typically done in the lifecycle method , as shown in the next code snippet:

```csharp
public override void OnCreate(Bundle savedState)
{
    base.OnCreate(savedState);
    SetHasOptionsMenu(true);
}
```

La schermata seguente mostra l'aspetto di questo menu:

[![Schermata di esempio dell'app My Trips che visualizza le voci di menu](creating-a-fragment-images/fragment-menu-example.png)](creating-a-fragment-images/fragment-menu-example.png#lightbox)
