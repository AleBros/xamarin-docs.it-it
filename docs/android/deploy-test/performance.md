---
title: Prestazioni di Xamarin.Android
description: "Esistono varie tecniche per incrementare le prestazioni delle applicazioni compilate con Xamarin.Android. Nel loro insieme, queste tecniche possono ridurre notevolmente il carico di lavoro di una CPU e la quantità di memoria usata da un'applicazione. Questo articolo descrive queste tecniche e le relative caratteristiche."
ms.topic: article
ms.prod: xamarin
ms.assetid: dc2e27f2-7f71-4d57-9cf9-165528276613
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/16/2018
ms.openlocfilehash: 3871955f723d1b3aec6245bba0502ca4f955d64c
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2018
---
# <a name="xamarinandroid-performance"></a>Prestazioni di Xamarin.Android

_Esistono varie tecniche per incrementare le prestazioni delle applicazioni compilate con Xamarin.Android. Nel loro insieme, queste tecniche possono ridurre notevolmente il carico di lavoro di una CPU e la quantità di memoria usata da un'applicazione. Questo articolo descrive queste tecniche e le relative caratteristiche._

## <a name="performance-overview"></a>Panoramica sulle prestazioni

Le prestazioni insoddisfacenti di un'applicazione si manifestano in molti modi. Può sembrare che l'applicazione non risponda, lo scorrimento diventa lento e si riduce la durata della batteria. Tuttavia, l'ottimizzazione delle prestazioni implica più della semplice implementazione di codice efficiente. Deve essere considerata anche l'esperienza dell'utente in termini di prestazioni dell'applicazione. Ad esempio, assicurarsi che le operazioni vengano eseguite senza impedire all'utente di eseguire altre attività può contribuire a migliorare l'esperienza dell'utente.

Esistono diverse tecniche per migliorare le prestazioni, e le prestazioni percepite, delle applicazioni compilate con Xamarin.Android. e comprendono:

- [Ottimizzare le gerarchie di layout](#optimizelayout)
- [Ottimizzare le visualizzazioni elenco](#optimizelistviews)
- [Rimuovere i gestori eventi nelle attività](#removeeventhandlers)
- [Limitare il ciclo di vita dei servizi](#limitservices)
- [Rilasciare le risorse in seguito a una notifica](#releaseresources)
- [Rilasciare le risorse quando l'interfaccia utente è nascosta](#releaseresourcesuihidden)
- [Ottimizzare le risorse immagine](#optimizeimages)
- [Eliminare le risorse immagine inutilizzate](#disposeimages)
- [Evitare calcoli aritmetici a virgola mobile](#avoidfloats)
- [Chiudere le finestre di dialogo](#dismissdialogs)


> [!NOTE]
> Prima di leggere questo articolo, è consigliabile vedere [Prestazioni multipiattaforma](~/cross-platform/deploy-test/memory-perf-best-practices.md), che illustra le tecniche non specifiche di una piattaforma che consentono di migliorare l'utilizzo della memoria e le prestazioni delle applicazioni compilate con la piattaforma Xamarin.

<a name="optimizelayout" />

## <a name="optimize-layout-hierarchies"></a>Ottimizzare le gerarchie di layout

Ogni layout aggiunto a un'applicazione richiede inizializzazione, layout e disegno. Il passaggio di layout può essere dispendioso in presenza di istanze di [`LinearLayout`](https://developer.xamarin.com/api/type/Android.Widget.LinearLayout/) annidate che usano il parametro `weight`, perché ogni elemento figlio verrà misurato due volte. L'uso di istanze annidate di `LinearLayout` può portare a una gerarchia di visualizzazione su molti livelli, con conseguente riduzione delle prestazioni per i layout sottoposti a inflating più volte, come in un [`ListView`](https://developer.xamarin.com/api/type/Android.Widget.ListView/). È pertanto importante ottimizzare questi layout, perché i vantaggi a livello di prestazioni risulteranno moltiplicati.

Si consideri ad esempio [`LinearLayout`](https://developer.xamarin.com/api/type/Android.Widget.LinearLayout/) per una riga di una visualizzazione elenco con un'icona, un titolo e una descrizione. `LinearLayout` conterrà un [`ImageView`](https://developer.xamarin.com/api/type/Android.Widget.ImageView/) e un `LinearLayout` verticale che contiene due istanze di [`TextView`](https://developer.xamarin.com/api/type/Android.Widget.TextView/):

```xml
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="fill_parent"
    android:layout_height="?android:attr/listPreferredItemHeight"
    android:padding="5dip">
    <ImageView
        android:id="@+id/icon"
        android:layout_width="wrap_content"
        android:layout_height="fill_parent"
        android:layout_marginRight="5dip"
        android:src="@drawable/icon" />
    <LinearLayout
        android:orientation="vertical"
        android:layout_width="0dip"
        android:layout_weight="1"
        android:layout_height="fill_parent">
        <TextView
            android:layout_width="fill_parent"
            android:layout_height="0dip"
            android:layout_weight="1"
            android:gravity="center_vertical"
            android:text="Mei tempor iuvaret ad." />
        <TextView
            android:layout_width="fill_parent"
            android:layout_height="0dip"
            android:layout_weight="1"
            android:singleLine="true"
            android:ellipsize="marquee"
            android:text="Lorem ipsum dolor sit amet." />
    </LinearLayout>
</LinearLayout>
```

Questo layout ha una profondità di 3 livelli e comporta sprechi quando viene sottoposto a inflating per ogni riga [`ListView`](https://developer.xamarin.com/api/type/Android.Widget.ListView/). Può tuttavia essere migliorato appiattendolo, come illustrato nell'esempio di codice seguente:

```xml
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="fill_parent"
    android:layout_height="?android:attr/listPreferredItemHeight"
    android:padding="5dip">
    <ImageView
        android:id="@+id/icon"
        android:layout_width="wrap_content"
        android:layout_height="fill_parent"
        android:layout_alignParentTop="true"
        android:layout_alignParentBottom="true"
        android:layout_marginRight="5dip"
        android:src="@drawable/icon" />
    <TextView
        android:id="@+id/secondLine"
        android:layout_width="fill_parent"
        android:layout_height="25dip"
        android:layout_toRightOf="@id/icon"
        android:layout_alignParentBottom="true"
        android:layout_alignParentRight="true"
        android:singleLine="true"
        android:ellipsize="marquee"
        android:text="Lorem ipsum dolor sit amet." />
    <TextView
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:layout_toRightOf="@id/icon"
        android:layout_alignParentRight="true"
        android:layout_alignParentTop="true"
        android:layout_above="@id/secondLine"
        android:layout_alignWithParentIfMissing="true"
        android:gravity="center_vertical"
        android:text="Mei tempor iuvaret ad." />
</RelativeLayout>
```

La gerarchia precedente di 3 livelli è stata ridotta a 2 livelli e un singolo [`RelativeLayout`](https://developer.xamarin.com/api/type/Android.Widget.RelativeLayout/) ha sostituito due istanze di [`LinearLayout`](https://developer.xamarin.com/api/type/Android.Widget.LinearLayout/). Si otterrà un aumento significativo delle prestazioni con l'inflating del layout per ogni riga di [`ListView`](https://developer.xamarin.com/api/type/Android.Widget.ListView/).

<a name="optimizelistviews" />

## <a name="optimize-list-views"></a>Ottimizzare le visualizzazioni elenco

Gli utenti si aspettano uno scorrimento fluido e tempi di caricamento rapidi per le istanze di [`ListView`](https://developer.xamarin.com/api/type/Android.Widget.ListView/). Le prestazioni di scorrimento possono tuttavia peggiorare quando ogni riga della visualizzazione elenco contiene gerarchie di visualizzazione con molti livelli di annidamento o quando le righe contengono layout complessi. Alcune tecniche consentono tuttavia di evitare un peggioramento delle prestazioni di `ListView`:

- Riutilizzare le visualizzazioni delle righe. Per altre informazioni, vedere [Riutilizzare le visualizzazioni delle righe](#reuserowviews).
- Appiattire i layout, laddove possibile.
- Memorizzare nella cache il contenuto delle righe recuperato da un servizio Web.
- Evitare il ridimensionamento delle immagini.

Nel loro insieme, queste tecniche consentono di mantenere uno scorrimento fluido per le istanze di [`ListView`](https://developer.xamarin.com/api/type/Android.Widget.ListView/).

<a name="reuserowviews" />

### <a name="reuse-row-views"></a>Riutilizzare le visualizzazioni delle righe

Se si visualizzano centinaia di righe in [`ListView`](https://developer.xamarin.com/api/type/Android.Widget.ListView/), sarebbe uno spreco di memoria creare centinaia di oggetti [`View`](https://developer.xamarin.com/api/type/Android.Views.View/) quando poi ne vengono visualizzati contemporaneamente sullo schermo solo alcuni. Solo gli oggetti `View` visibili nelle righe sullo schermo possono essere invece caricati in memoria e il **contenuto** viene caricato in questi oggetti riutilizzati. Questo impedisce la creazione di istanze di centinaia di oggetti aggiuntivi, con conseguente risparmio di tempo e memoria.

Quando una riga viene rimossa dallo schermo, quindi, la visualizzazione può essere inserita in una coda per essere riutilizzata in seguito, come illustrato nell'esempio di codice seguente:

```csharp
public override View GetView(int position, View convertView, ViewGroup parent)
{
   View view = convertView; // re-use an existing view, if one is supplied
   if (view == null) // otherwise create a new one
       view = context.LayoutInflater.Inflate(Android.Resource.Layout.SimpleListItem1, null);
   // set view properties to reflect data for the given row
   view.FindViewById<TextView>(Android.Resource.Id.Text1).Text = items[position];
   // return the view, populated with data, for display
   return view;
}
```

Mentre l'utente scorre, [`ListView`](https://developer.xamarin.com/api/type/Android.Widget.ListView/) chiama l'override di `GetView` per richiedere nuove visualizzazioni da mostrare. Se disponibili, passa una visualizzazione inutilizzata nel parametro `convertView`. Se questo valore è `null`, il codice crea una nuova istanza di [`View`](https://developer.xamarin.com/api/type/Android.Views.View/). In caso contrario, le proprietà `convertView` possono essere reimpostate e riutilizzate.

Per altre informazioni, vedere [Row View Re-Use](~/android/user-interface/layouts/list-view/populating.md) (Riutilizzo della visualizzazione delle righe) in [Populating a ListView with Data](~/android/user-interface/layouts/list-view/populating.md) (Popolamento dei dati in ListView).

<a name="removeeventhandlers" />

## <a name="remove-event-handlers-in-activities"></a>Rimuovere i gestori eventi nelle attività

Quando un'attività viene eliminata definitivamente nel runtime di Android, potrebbe essere ancora attiva nel runtime di Mono. Rimuovere quindi i gestori eventi per gli oggetti esterni in `Activity.OnPause` per evitare che il runtime mantenga un riferimento a un'attività eliminata.

In un'attività, dichiarare i gestori eventi a livello di classe:

```csharp
EventHandler<UpdatingEventArgs> service1UpdateHandler;
```

Implementare quindi i gestori nell'attività, come in `OnResume`:

```csharp
service1UpdateHandler = (object s, UpdatingEventArgs args) => {
    this.RunOnUiThread (() => {
        this.updateStatusText1.Text = args.Message;
    });
};
App.Current.Service1.Updated += service1UpdateHandler;
```

Quando l'attività esce dallo stato di esecuzione, viene chiamato l'evento `OnPause`. Nell'implementazione di `OnPause` rimuovere i gestori, come indicato di seguito:

```csharp
App.Current.Service1.Updated -= service1UpdateHandler;
```

<a name="limitservices" />

## <a name="limit-the-lifespan-of-services"></a>Limitare il ciclo di vita dei servizi

Quando un servizio viene avviato, Android mantiene il processo del servizio in esecuzione. L'esecuzione del processo diventa in questo modo dispendiosa a livello di risorse, perché la memoria usata non può essere sottoposta a paging oppure usata altrove. Lasciare un servizio in esecuzione quando non è necessario aumenta pertanto il rischio di prestazioni insufficienti per un'applicazione a causa dei vincoli di memoria. Anche il cambio di applicazione può risultare meno efficiente, a causa della riduzione del numero di processi che Android può memorizzare nella cache.

Il ciclo di vita di un servizio può essere limitato tramite `IntentService`, che termina dopo aver gestito l'intent che l'ha avviato.

<a name="releaseresources" />

## <a name="release-resources-when-notified"></a>Rilasciare le risorse in seguito a una notifica

Durante il ciclo di vita dell'applicazione, il callback [`OnTrimMemory`](https://developer.xamarin.com/api/member/Android.App.Activity.OnTrimMemory/p/Android.Content.TrimMemory/) fornisce una notifica quando la memoria del dispositivo è insufficiente. Questo callback deve essere implementato per l'ascolto delle notifiche relative al livello di memoria seguenti:

- [`TrimMemoryRunningModerate`](https://developer.xamarin.com/api/field/Android.Content.ComponentCallbacks2.TrimMemoryRunningModerate/): l'applicazione *potrebbe* voler rilasciare alcune risorse non necessarie.
- [`TrimMemoryRunningLow`](https://developer.xamarin.com/api/field/Android.Content.ComponentCallbacks2.TrimMemoryRunningLow/): l'applicazione *deve* rilasciare le risorse non necessarie.
- [`TrimMemoryRunningCritical`](https://developer.xamarin.com/api/field/Android.Content.ComponentCallbacks2.TrimMemoryRunningCritical/): l'applicazione *deve* rilasciare il maggior numero di processi non critici possibile.

Inoltre, quando il processo dell'applicazione viene memorizzato nella cache, le notifiche del livello di memoria seguenti possono essere ricevute dal callback [`OnTrimMemory`](https://developer.xamarin.com/api/member/Android.App.Activity.OnTrimMemory/p/Android.Content.TrimMemory/):

- [`TrimMemoryBackground`](https://developer.xamarin.com/api/field/Android.Content.ComponentCallbacks2.TrimMemoryBackground/): rilasciare le risorse che possono essere ricreate velocemente e in modo efficiente se l'utente torna all'app.
- [`TrimMemoryModerate`](https://developer.xamarin.com/api/field/Android.Content.ComponentCallbacks2.TrimMemoryModerate/): il rilascio delle risorse può consentire al sistema di mantenere altri processi memorizzati nella cache per un miglioramento complessivo delle prestazioni.
- [`TrimMemoryComplete`](https://developer.xamarin.com/api/field/Android.Content.ComponentCallbacks2.TrimMemoryComplete/): il processo dell'applicazione verrà terminato presto se non si recupera velocemente ulteriore memoria.

Occorre rispondere alle notifiche rilasciando le risorse in base al livello ricevuto.

<a name="releaseresourcesuihidden" />

## <a name="release-resources-when-the-user-interface-is-hidden"></a>Rilasciare le risorse quando l'interfaccia utente è nascosta

Rilasciare le eventuali risorse usate dall'interfaccia utente dell'app quando l'utente passa a un'altra app, perché ciò può aumentare notevolmente la capacità di Android per i processi memorizzati nella cache, con conseguenti effetti potenziali sulla qualità dell'esperienza utente.

Per ricevere una notifica quando l'utente esce dall'interfaccia utente, implementare il callback [`OnTrimMemory`](https://developer.xamarin.com/api/member/Android.App.Activity.OnTrimMemory/p/Android.Content.TrimMemory/) nelle classi `Activity` e restare in ascolto del livello [`TrimMemoryUiHidden`](https://developer.xamarin.com/api/field/Android.Content.ComponentCallbacks2.TrimMemoryUiHidden/), che indica che l'interfaccia utente è nascosta dalla visualizzazione. Questa notifica viene ricevuta solo quando *tutti* i componenti dell'interfaccia utente dell'applicazione sono nascosti all'utente. Il rilascio delle risorse dell'interfaccia utente quando si riceve questa notifica garantisce che le risorse dell'interfaccia utente siano ancora disponibili per riprendere l'attività se l'utente torna all'interfaccia utente da un'altra attività nell'app.

<a name="optimizeimages" />

## <a name="optimize-image-resources"></a>Ottimizzare le risorse immagine

Le immagini sono tra le risorse più dispendiose usate dalle applicazioni e spesso vengono acquisite a risoluzioni elevate. Pertanto, per la visualizzazione di un'immagine scegliere la risoluzione necessaria per lo schermo del dispositivo. Se l'immagine ha una risoluzione più alta rispetto allo schermo, deve essere ridimensionata.

Per altre informazioni, vedere [Ottimizzare le risorse immagine](~/cross-platform/deploy-test/memory-perf-best-practices.md#optimizeimages) nella guida [Prestazioni multipiattaforma](~/cross-platform/deploy-test/memory-perf-best-practices.md).

<a name="disposeimages" />

## <a name="dispose-of-unused-image-resources"></a>Eliminare le risorse immagine inutilizzate

Per risparmiare memoria, è consigliabile eliminare le risorse immagine di grandi dimensioni non più necessarie. Tuttavia, è importante assicurarsi che le immagini vengano eliminate correttamente. Invece di usare una chiamata esplicita di `.Dispose()`, è possibile sfruttare le istruzioni [using](https://docs.microsoft.com/en-us/dotnet/csharp/language-reference/keywords/using-statement) per garantire un utilizzo corretto degli oggetti `IDisposable`. 

Ad esempio, la classe [Bitmap](https://developer.xamarin.com/api/type/Android.Graphics.Bitmap/) implementa `IDisposable`. Il wrapping della creazione dell'istanza di un oggetto `BitMap` in un blocco `using` ne garantisce la corretta eliminazione all'uscita dal blocco:

```csharp
using (Bitmap smallPic = BitmapFactory.DecodeByteArray(smallImageByte, 0, smallImageByte.Length))
{
    // Use the smallPic bit map here
}
```

Per altre informazioni sul rilascio delle risorse eliminabili, vedere [Release IDisposable Resources](~/cross-platform/deploy-test/memory-perf-best-practices.md#idisposable) (Rilasciare risorse IDisposable).  


<a name="avoidfloats" />

## <a name="avoid-floating-point-arithmetic"></a>Evitare calcoli aritmetici a virgola mobile

Nei dispositivi Android i calcoli aritmetici a virgola mobile sono circa due volte più lenti rispetto ai calcoli di interi. Sostituire quindi i calcoli a virgola mobile con i calcoli di interi se possibile. Non esiste tuttavia alcuna differenza a livello di tempo di esecuzione tra l'aritmetica `float` e `double` su hardware recente.

> [!NOTE]
> Anche per i calcoli di interi, alcune CPU non supportano funzionalità di divisione hardware. Le divisioni di interi e le operazioni di modulo vengono pertanto spesso eseguite nel software.

<a name="dismissdialogs" />

## <a name="dismiss-dialogs"></a>Chiudere le finestre di dialogo

Quando si usa la classe [`ProgressDialog`](https://developer.xamarin.com/api/type/Android.App.ProgressDialog/) (o qualsiasi finestra di dialogo o avviso), invece di chiamare il metodo [`Hide`](https://developer.xamarin.com/api/member/Android.App.Dialog.Hide()/) una volta soddisfatti gli scopi della finestra di dialogo, chiamare il metodo [`Dismiss`](https://developer.xamarin.com/api/member/Android.App.Dialog.Dismiss()/). In caso contrario, la finestra di dialogo rimane attiva causando perdite di risorse a causa del riferimento in essere all'attività.

## <a name="summary"></a>Riepilogo

Questo articolo ha descritto e illustrato le tecniche per incrementare le prestazioni delle applicazioni create con Xamarin.Android. Nel loro insieme, queste tecniche possono ridurre notevolmente il carico di lavoro di una CPU e la quantità di memoria usata da un'applicazione.


## <a name="related-links"></a>Collegamenti correlati

- [Prestazioni multipiattaforma](~/cross-platform/deploy-test/memory-perf-best-practices.md)
