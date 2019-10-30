---
title: Creazione di un frammento
ms.prod: xamarin
ms.assetid: F2997242-BC29-1440-7F1A-CFC447CD73FA
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/07/2018
ms.openlocfilehash: 0e8d3748c7ddd337cf2f27f5b272b208e79d503a
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73027508"
---
# <a name="creating-a-fragment"></a>Creazione di un frammento

Per creare un frammento, una classe deve ereditare da `Android.App.Fragment` e quindi eseguire l'override del metodo `OnCreateView`. `OnCreateView` verrà chiamata dall'attività host quando è il momento di inserire il frammento sullo schermo e restituirà una `View`. Un `OnCreateView` tipico creerà questo `View` comportando la creazione di un file di layout e la relativa associazione a un contenitore padre. Le caratteristiche del contenitore sono importanti perché Android applicherà i parametri del layout dell'elemento padre all'interfaccia utente del frammento. Questa condizione è illustrata nell'esempio che segue.

```csharp
public override View OnCreateView(LayoutInflater inflater, ViewGroup container, Bundle savedInstanceState)
{
    return inflater.Inflate(Resource.Layout.Example_Fragment, container, false);
}
```

Il codice precedente consente di ingrandire la vista `Resource.Layout.Example_Fragment`e di aggiungerla come visualizzazione figlio al contenitore di `ViewGroup`.

> [!NOTE]
> Le sottoclassi del frammento devono avere un costruttore di nessun argomento predefinito pubblico.

## <a name="adding-a-fragment-to-an-activity"></a>Aggiunta di un frammento a un'attività

È possibile che un frammento sia ospitato all'interno di un'attività in due modi:

- I frammenti **Dichiarativi** &ndash; possono essere usati in modo dichiarativo all'interno di `.axml` file di layout usando il tag `<Fragment>`.

- È anche possibile creare un'istanza **a livello di codice** &ndash; frammenti in modo dinamico usando l'API della classe `FragmentManager`.

L'utilizzo programmatico tramite la classe `FragmentManager` verrà illustrato più avanti in questa guida.

### <a name="using-a-fragment-declaratively"></a>Uso dichiarativo di un frammento

Per aggiungere un frammento all'interno del layout è necessario usare il tag `<fragment>` e quindi identificare il frammento fornendo l'attributo `class` o l'attributo `android:name`. Il frammento di codice seguente illustra come usare l'attributo `class` per dichiarare una `fragment`:

```xml
<?xml version="1.0" encoding="utf-8"?>
<fragment class="com.xamarin.sample.fragments.TitlesFragment"
            android:id="@+id/titles_fragment"
            android:layout_width="fill_parent"
            android:layout_height="fill_parent" />
```

Questo frammento di codice seguente illustra come dichiarare un `fragment` usando l'attributo `android:name` per identificare la classe fragment:

```xml
<?xml version="1.0" encoding="utf-8"?>
<fragment android:name="com.xamarin.sample.fragments.TitlesFragment"
            android:id="@+id/titles_fragment"
            android:layout_width="fill_parent"
            android:layout_height="fill_parent" />
```

Quando viene creata l'attività, Android creerà un'istanza di ogni frammento specificato nel file di layout e inserirà la vista creata da `OnCreateView` al posto dell'elemento `Fragment`.
I frammenti che vengono aggiunti in modo dichiarativo a un'attività sono statici e rimarranno sull'attività fino a quando non vengono eliminati. non è possibile sostituire o rimuovere in modo dinamico tale frammento durante il ciclo di vita dell'attività a cui è collegato.

A ogni frammento deve essere assegnato un identificatore univoco:

- **Android: id** &ndash; come con altri elementi dell'interfaccia utente in un file di layout, si tratta di un ID univoco.

- **Android: tag** &ndash; questo attributo è una stringa univoca.

Se non viene usato nessuno dei due metodi precedenti, il frammento presuppone l'ID della visualizzazione del contenitore. Nell'esempio seguente, in cui non viene fornito né `android:id` né `android:tag`, Android assegnerà l'ID `fragment_container` al frammento:

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

### <a name="package-name-case"></a>Case nome pacchetto

Android non consente caratteri maiuscoli nei nomi dei pacchetti; verrà generata un'eccezione quando si tenta di ingrandire la vista se il nome di un pacchetto contiene un carattere maiuscolo. Tuttavia, Novell. Android è più indulgente e tollera i caratteri maiuscoli nello spazio dei nomi.

Ad esempio, entrambi i frammenti di codice seguenti funzioneranno con Novell. Android. Tuttavia, il secondo frammento di codice causerà la generazione di un `android.view.InflateException` da parte di un'applicazione Android pura basata su Java.

```xml
<fragment class="com.example.DetailsFragment" android:id="@+id/fragment_content" android:layout_width="match_parent" android:layout_height="match_parent" />
```

OR

```xml
<fragment class="Com.Example.DetailsFragment" android:id="@+id/fragment_content" android:layout_width="match_parent" android:layout_height="match_parent" />
```

## <a name="fragment-lifecycle"></a>Ciclo di vita del frammento

Il ciclo di vita dei frammenti è indipendente dal ciclo di vita [dell'attività di hosting](~/android/app-fundamentals/activity-lifecycle/index.md).
Ad esempio, quando un'attività viene sospesa, tutti i frammenti associati vengono sospesi. Il diagramma seguente illustra il ciclo di vita del frammento.

[diagramma di flusso![che illustra il ciclo di vita del frammento](creating-a-fragment-images/fragment-lifecycle.png)](creating-a-fragment-images/fragment-lifecycle.png#lightbox)

### <a name="fragment-creation-lifecycle-methods"></a>Metodi del ciclo di vita della creazione di frammenti

L'elenco seguente illustra il flusso dei diversi callback nel ciclo di vita di un frammento durante la creazione:

- **`OnInflate()`** &ndash; chiamato quando il frammento viene creato come parte di un layout di visualizzazione. Questa operazione può essere chiamata immediatamente dopo la creazione dichiarativa del frammento da un file di layout XML. Il frammento non è ancora associato all'attività, ma l' **attività**, il bundle e il **set**di **attributi** dalla gerarchia di visualizzazione vengono passati come parametri. Questo metodo è particolarmente utile per l'analisi dell' **attributo** e per il salvataggio degli attributi che potrebbero essere utilizzati in un secondo momento dal frammento.

- **`OnAttach()`** &ndash; chiamato dopo che il frammento è associato all'attività. Si tratta del primo metodo da eseguire quando il frammento è pronto per essere utilizzato. In generale, i frammenti non devono implementare un costruttore o eseguire l'override del costruttore predefinito. Tutti i componenti necessari per il frammento devono essere inizializzati in questo metodo.

- **`OnCreate()`** &ndash; chiamato dall'attività per creare il frammento. Quando viene chiamato questo metodo, non è possibile creare un'istanza completa della gerarchia di visualizzazione dell'attività host, quindi il frammento non deve basarsi su alcuna parte della gerarchia di visualizzazione dell'attività fino a un momento successivo nel ciclo di vita del frammento. Ad esempio, non usare questo metodo per eseguire modifiche o modifiche all'interfaccia utente dell'applicazione. Questa è la prima volta in cui il frammento può iniziare a raccogliere i dati necessari. Il frammento viene eseguito nel thread dell'interfaccia utente in questo punto, evitando così la lunga elaborazione o eseguendo tale elaborazione in un thread in background. Questo metodo può essere ignorato se viene chiamato **SetRetainInstance (true)** .
    Questa alternativa verrà descritta in dettaglio più avanti.

- **`OnCreateView()`** &ndash; crea la visualizzazione per il frammento.
    Questo metodo viene chiamato dopo il completamento del metodo **OnCreate ()** dell'attività. A questo punto, è possibile interagire in modo sicuro con la gerarchia di visualizzazione dell'attività. Questo metodo deve restituire la vista che verrà usata dal frammento.

- **`OnActivityCreated()`** &ndash; chiamato dopo che **Activity. OnCreate** è stato completato dall'attività di hosting.
    Le modifiche finali apportate all'interfaccia utente devono essere eseguite in questo momento.

- **`OnStart()`** &ndash; chiamato dopo che l'attività contenitore è stata ripresa. In questo modo, il frammento viene reso visibile all'utente. In molti casi, il frammento conterrà codice che altrimenti sarebbe presente nel metodo **OnStart ()** di un'attività.

- **`OnResume()`** &ndash; questo è l'ultimo metodo chiamato prima che l'utente possa interagire con il frammento. Un esempio del tipo di codice che deve essere eseguito in questo metodo è l'abilitazione di funzionalità di un dispositivo con cui l'utente può interagire, ad esempio la fotocamera che i servizi di posizione. I servizi come questi possono causare un esaurimento della batteria, tuttavia, e un'applicazione dovrebbe ridurre al minimo l'uso per preservare la durata della batteria.

### <a name="fragment-destruction-lifecycle-methods"></a>Metodi ciclo di vita distruzione frammenti

Nell'elenco seguente vengono illustrati i metodi del ciclo di vita che vengono chiamati come frammento che viene eliminato:

- **`OnPause()`** &ndash; l'utente non è più in grado di interagire con il frammento. Questa situazione è dovuta al fatto che un'altra operazione Fragment sta modificando il frammento oppure l'attività di hosting è sospesa. È possibile che l'attività che ospita questo frammento possa essere ancora visibile, ovvero che l'attività in stato attivo sia parzialmente trasparente o non occupi lo schermo intero. Quando questo metodo diventa attivo, è la prima indicazione che l'utente sta lasciando il frammento. Il frammento deve salvare le modifiche.

- **`OnStop()`** &ndash; il frammento non è più visibile. L'attività host può essere arrestata o un'operazione di frammento la modifica nell'attività. Questo callback funziona allo stesso modo di **Activity. OnStop**.

- **`OnDestroyView()`** &ndash; questo metodo viene chiamato per pulire le risorse associate alla visualizzazione. Questo metodo viene chiamato quando la visualizzazione associata al frammento è stata eliminata definitivamente.

- **`OnDestroy()`** &ndash; questo metodo viene chiamato quando il frammento non è più in uso. È ancora associato all'attività, ma il frammento non è più funzionante. Questo metodo deve rilasciare tutte le risorse usate dal frammento, ad esempio un [**SurfaceView**](xref:Android.Views.SurfaceView) che può essere usato per una fotocamera. Questo metodo può essere ignorato se viene chiamato **SetRetainInstance (true)** . Questa alternativa verrà descritta in dettaglio più avanti.

- **`OnDetach()`** &ndash; questo metodo viene chiamato immediatamente prima che il frammento non sia più associato all'attività. La gerarchia di visualizzazione del frammento non esiste più e tutte le risorse usate dal frammento devono essere rilasciate a questo punto.

### <a name="using-setretaininstance"></a>Uso di SetRetainInstance

È possibile che un frammento specifichi che non deve essere completamente distrutto se l'attività viene ricreata. La classe `Fragment` fornisce il metodo `SetRetainInstance` a questo scopo. Se `true` viene passato a questo metodo, quando l'attività viene riavviata, verrà utilizzata la stessa istanza del frammento. In tal caso, verranno richiamati tutti i metodi di callback ad eccezione dei callback del ciclo di `OnCreate` e del `OnDestroy`. Questo processo è illustrato nel diagramma del ciclo di vita illustrato sopra (dalle linee tratteggiate verdi).

## <a name="fragment-state-management"></a>Gestione dello stato dei frammenti

I frammenti possono salvare e ripristinare lo stato durante il ciclo di vita del frammento usando un'istanza di una `Bundle`. Il bundle consente a un frammento di salvare i dati come coppie chiave/valore ed è utile per dati semplici che non richiedono molta memoria. Un frammento può salvare il proprio stato con una chiamata a `OnSaveInstanceState`:

```csharp
public override void OnSaveInstanceState(Bundle outState)
{
    base.OnSaveInstanceState(outState);
    outState.PutInt("current_choice", _currentCheckPosition);
}
```

Quando viene creata una nuova istanza di un frammento, lo stato salvato nell'`Bundle` diventerà disponibile per la nuova istanza tramite i metodi `OnCreate`, `OnCreateView`e `OnActivityCreated` della nuova istanza.
Nell'esempio seguente viene illustrato come recuperare il valore `current_choice` dal `Bundle`:

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

L'override di `OnSaveInstanceState` è un meccanismo appropriato per salvare i dati temporanei in un frammento tra le modifiche dell'orientamento, ad esempio il valore `current_choice` nell'esempio precedente. Tuttavia, l'implementazione predefinita di `OnSaveInstanceState` si occupa di salvare i dati temporanei nell'interfaccia utente per ogni visualizzazione a cui è assegnato un ID. Si osservi, ad esempio, un'applicazione con un elemento `EditText` definito in XML, come indicato di seguito:

```xml
<EditText android:id="@+id/myText"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"/>
```

Poiché il controllo `EditText` dispone di un `id` assegnato, il frammento Salva automaticamente i dati nel widget quando viene chiamato `OnSaveInstanceState`.

### <a name="bundle-limitations"></a>Limitazioni del bundle

Sebbene l'utilizzo di `OnSaveInstanceState` facilita il salvataggio di dati temporanei, l'utilizzo di questo metodo presenta alcune limitazioni:

- Se il frammento non viene aggiunto allo stack indietro, il suo stato non verrà ripristinato quando l'utente preme il pulsante **indietro** .

- Quando si usa il bundle per salvare i dati, i dati vengono serializzati. Questo può causare ritardi di elaborazione.

## <a name="contributing-to-the-menu"></a>Aggiunta come contributo al menu

I frammenti possono fornire elementi al menu dell'attività di hosting.
Un'attività gestisce innanzitutto le voci di menu. Se l'attività non dispone di un gestore, l'evento verrà passato al frammento, che lo gestirà.

Per aggiungere elementi al menu dell'attività, un frammento deve eseguire due operazioni.
Prima di tutto, il frammento deve implementare il metodo `OnCreateOptionsMenu` e inserire gli elementi nel menu, come illustrato nel codice seguente:

```csharp
public override void OnCreateOptionsMenu(IMenu menu, MenuInflater menuInflater)
{
    menuInflater.Inflate(Resource.Menu.menu_fragment_vehicle_list, menu);
    base.OnCreateOptionsMenu(menu, menuInflater);
}
```

Il menu del frammento di codice precedente viene ingrandito dall'XML seguente, che si trova nel file `menu_fragment_vehicle_list.xml`:

```xml
<?xml version="1.0" encoding="utf-8"?>
<menu xmlns:android="http://schemas.android.com/apk/res/android">
  <item android:id="@+id/add_vehicle"
        android:icon="@drawable/ic_menu_add_data"
        android:title="@string/add_vehicle" />
</menu>
```

Successivamente, il frammento deve chiamare `SetHasOptionsMenu(true)`. La chiamata a questo metodo annuncia ad Android che il frammento include voci di menu che contribuiscono al menu opzioni. A meno che non venga effettuata la chiamata a questo metodo, le voci di menu per il frammento non verranno aggiunte al menu delle opzioni dell'attività. Questa operazione viene in genere eseguita nel metodo del ciclo di vita `OnCreate()`, come illustrato nel frammento di codice seguente:

```csharp
public override void OnCreate(Bundle savedState)
{
    base.OnCreate(savedState);
    SetHasOptionsMenu(true);
}
```

La schermata seguente mostra come dovrebbe apparire questo menu:

[schermata di esempio![dell'app My trips che visualizza le voci di menu](creating-a-fragment-images/fragment-menu-example.png)](creating-a-fragment-images/fragment-menu-example.png#lightbox)
