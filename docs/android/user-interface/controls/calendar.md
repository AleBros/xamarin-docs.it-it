---
title: Calendar
ms.topic: article
ms.prod: xamarin
ms.assetid: 78666541-CA14-4CD8-A94A-A9621C57813E
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/06/2018
ms.openlocfilehash: 8075473464472c5a830f62ebfc91c00ad54d1b98
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2018
---
# <a name="calendar"></a>Calendar

<a name="Calendar_API" />

## <a name="calendar-api"></a>Calendario API

Un nuovo set di API introdotte in Android 4 calendario supporta le applicazioni che sono progettate per leggere o scrivere dati al provider di calendario. Queste API supportano una vasta gamma di opzioni di interazione con i dati di calendario, inclusa la possibilità di leggere e scrivere gli eventi, i partecipanti e i promemoria. Tramite il provider di calendario nell'applicazione, i dati che aggiunti tramite l'API verranno visualizzato nell'app del calendario predefinito che viene fornito con Android 4.

<a name="Adding_Permissions" />

## <a name="adding-permissions"></a>L'aggiunta di autorizzazioni

Quando si utilizza il nuovo calendario API dell'applicazione, la prima cosa che devi fare è aggiungere le autorizzazioni appropriate per il manifesto Android. Le autorizzazioni è necessario aggiungere sono `android.permisson.READ_CALENDAR` e `android.permission.WRITE_CALENDAR`, a seconda se la lettura e/o la scrittura dei dati di calendario.

<a name="Using_the_Calendar_Contract" />

## <a name="using-the-calendar-contract"></a>Utilizzo del contratto di calendario

Dopo aver impostato le autorizzazioni, è possibile interagire con i dati del calendario utilizzando il `CalendarContract` classe. Questa classe fornisce un modello di dati utilizzabili dalle applicazioni quando interagiscono con il provider di calendario. Il `CalendarContract` consente alle applicazioni di risolvere l'URI per l'entità di calendario, ad esempio calendari ed eventi. Fornisce inoltre un modo per interagire con i diversi campi in tutte le entità, ad esempio un calendario nome e ID, o un evento data di inizio e fine.

Esaminiamo un esempio che usa l'API del calendario. In questo esempio, si esamineranno come enumerare i calendari e i relativi eventi, nonché come aggiungere un nuovo evento a un calendario.

<a name="Listing_Calendars" />

## <a name="listing-calendars"></a>Elenco di calendari

In primo luogo, si esamineranno come enumerare i calendari che sono stati registrati nell'app del calendario. A tale scopo, è possibile creare un'istanza di un `CursorLoader`. Introdotto in Android 3.0 (API 11), `CursorLoader` è il modo migliore per utilizzare un `ContentProvider`. Come minimo, è necessario specificare l'Uri del contenuto per i calendari e le colonne che si desidera da restituire. Specifica di questa colonna è noto come un _proiezione_.

La chiamata di `CursorLoader.LoadInBackground` metodo consente di eseguire query di un provider di contenuti per i dati, ad esempio il provider di calendario.
`LoadInBackground` esegue l'operazione di caricamento effettivo e restituisce un `Cursor` con i risultati della query.

Il `CalendarContract` ci consente di specificare il contenuto `Uri` e proiezione. Per ottenere il contenuto `Uri` per le query calendari, è possibile utilizzare il `CalendarContract.Calendars.ContentUri` proprietà simile al seguente:

```csharp
var calendarsUri = CalendarContract.Calendars.ContentUri;
```

Utilizzo di `CalendarContract` per specificare il calendario è altrettanto semplice colonne che si desidera. È sufficiente aggiungere campi di `CalendarContract.Calendars.InterfaceConsts` classe a una matrice. Ad esempio, il codice seguente include l'ID del calendario, il nome visualizzato e il nome account:

```csharp
string[] calendarsProjection = {
    CalendarContract.Calendars.InterfaceConsts.Id,
    CalendarContract.Calendars.InterfaceConsts.CalendarDisplayName,
    CalendarContract.Calendars.InterfaceConsts.AccountName
};
```

Il `Id` è importante includere se si utilizza un `SimpleCursorAdapter` per associare i dati per l'interfaccia utente, come si vedrà a breve. Con l'Uri di contenuto e proiezione sul posto, si crea un'istanza di `CursorLoader` e chiamare il `CursorLoader.LoadInBackground` per restituire un cursore con i dati di calendario, come illustrato di seguito:

```csharp
var loader = new CursorLoader(this, calendarsUri, calendarsProjection, null, null, null);
var cursor = (ICursor)loader.LoadInBackground();

```

L'interfaccia utente per questo esempio contiene un `ListView`, con ogni elemento nell'elenco che rappresenta un singolo calendario. Il codice XML seguente viene illustrato il markup che include il `ListView`:

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
android:orientation="vertical"
android:layout_width="fill_parent"
android:layout_height="fill_parent">
  <ListView
    android:id="@android:id/android:list"
    android:layout_width="fill_parent"
    android:layout_height="wrap_content" />
</LinearLayout>
```

Inoltre, è necessario specificare l'interfaccia utente per ogni elemento di elenco che si inserisce in un file XML separato, come indicato di seguito:

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
android:orientation="vertical"
android:layout_width="fill_parent"
android:layout_height="wrap_content">
  <TextView android:id="@+id/calDisplayName"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:textSize="16dip" />
  <TextView android:id="@+id/calAccountName"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:textSize="12dip" />
</LinearLayout>
```

Da questo momento, è sufficiente normale codice Android per associare i dati dalla posizione del cursore all'interfaccia utente. Si userà un `SimpleCursorAdapter` come indicato di seguito:

```csharp
string[] sourceColumns = {
    CalendarContract.Calendars.InterfaceConsts.CalendarDisplayName,
    CalendarContract.Calendars.InterfaceConsts.AccountName };

int[] targetResources = {
    Resource.Id.calDisplayName, Resource.Id.calAccountName };      

SimpleCursorAdapter adapter = new SimpleCursorAdapter (this,
    Resource.Layout.CalListItem, cursor, sourceColumns, targetResources);

ListAdapter = adapter;
```

Nel codice precedente, l'adapter accetta le colonne specificate nella `sourceColumns` della matrice e li scrive in elementi dell'interfaccia utente nel `targetResources` matrice per ogni voce del calendario del cursore. L'attività utilizzato in questo argomento è una sottoclasse di `ListActivity`; include la `ListAdapter` proprietà a cui è impostata l'adapter.

Di seguito viene riportata una schermata che mostra il risultato finale, con le informazioni del calendario visualizzata nel `ListView`:

[![CalendarDemo in esecuzione nell'emulatore, la visualizzazione di due voci di calendario](calendar-images/11-calendar.png)](calendar-images/11-calendar.png)


<a name="Listing_Calendar_Events" />

## <a name="listing-calendar-events"></a>Elenco eventi di calendario

Successiva vengono descritte le modalità enumerare gli eventi per un determinato calendario.
Basandosi sull'esempio precedente, si sarà presente un elenco di eventi quando l'utente seleziona uno dei calendari. Pertanto, sarà necessario gestire la selezione di elementi nel codice precedente:

```csharp
ListView.ItemClick += (sender, e) => {
    int i = (e as ItemEventArgs).Position;

    cursor.MoveToPosition(i);
    int calId =
        cursor.GetInt (cursor.GetColumnIndex (calendarsProjection [0]));

    var showEvents = new Intent(this, typeof(EventListActivity));
    showEvents.PutExtra("calId", calId);
    StartActivity(showEvents);
};
```

In questo codice, si sta creando un tentativo di aprire un'attività di tipo `EventListActivity`, passando la finalità dell'ID del calendario. È necessario l'ID di sapere quale calendario per query per gli eventi. Nel `EventListActivity`del `OnCreate` (metodo), è possibile recuperare l'ID di `Intent` come illustrato di seguito:

```csharp
_calId = Intent.GetIntExtra ("calId", -1);
```

Ora si gli eventi di query per questo calendario ID. Il processo di query per gli eventi è simile al modo in cui è richiesto l'elenco dei calendari in precedenza, solo questa volta lavoriamo con la `CalendarContract.Events` classe. Il codice seguente crea una query per recuperare gli eventi:

```csharp
var eventsUri = CalendarContract.Events.ContentUri;

string[] eventsProjection = {
    CalendarContract.Events.InterfaceConsts.Id,
    CalendarContract.Events.InterfaceConsts.Title,
    CalendarContract.Events.InterfaceConsts.Dtstart
};

var loader = new CursorLoader(this, eventsUri, eventsProjection,
                   String.Format ("calendar_id={0}", _calId), null, "dtstart ASC");
var cursor = (ICursor)loader.LoadInBackground();
```

In questo codice, è innanzitutto ottenere il contenuto `Uri` per gli eventi di `CalendarContract.Events.ContentUri` proprietà. È quindi possibile specificare le colonne di eventi che si desidera recuperare nella matrice eventsProjection.
Infine, è creare un'istanza di un `CursorLoader` con queste informazioni e il caricatore della chiamata `LoadInBackground` per restituire un `Cursor` con i dati dell'evento.

Per visualizzare i dati dell'evento nell'interfaccia utente, è possibile utilizzare markup e codice come fatto in precedenza per visualizzare l'elenco dei calendari. Nuovamente, utilizziamo `SimpleCursorAdapter` per associare i dati per un `ListView` come illustrato nel codice seguente:

```csharp
string[] sourceColumns = {
    CalendarContract.Events.InterfaceConsts.Title,
    CalendarContract.Events.InterfaceConsts.Dtstart };

int[] targetResources = {
    Resource.Id.eventTitle,
    Resource.Id.eventStartDate };

var adapter = new SimpleCursorAdapter (this, Resource.Layout.EventListItem,
    cursor, sourceColumns, targetResources);

adapter.ViewBinder = new ViewBinder ();       
ListAdapter = adapter;
```

La differenza principale tra il codice e il codice che è utilizzate in precedenza per visualizzare l'elenco di calendario è l'utilizzo di un `ViewBinder`, questo valore è impostato sulla riga:

```csharp
adapter.ViewBinder = new ViewBinder ();
```

La `ViewBinder` classe consente di controllare ulteriormente è come associare i valori per le visualizzazioni. In questo caso, è utilizzarlo per convertire una stringa di data, ora di inizio dell'evento da millisecondi come illustrato nell'implementazione seguente:

```csharp
class ViewBinder : Java.Lang.Object, SimpleCursorAdapter.IViewBinder
{    
    public bool SetViewValue (View view, Android.Database.ICursor cursor,
        int columnIndex)
    {
        if (columnIndex == 2) {
            long ms = cursor.GetLong (columnIndex);

            DateTime date = new DateTime (1970, 1, 1, 0, 0, 0,
                DateTimeKind.Utc).AddMilliseconds (ms).ToLocalTime ();

            TextView textView = (TextView)view;
            textView.Text = date.ToLongDateString ();

            return true;
        }
        return false;
    }    
}
```

Ciò consente di visualizzare un elenco di eventi come illustrato di seguito:

[![Schermata dell'app di esempio la visualizzazione di tre eventi del calendario](calendar-images/12-events.png)](calendar-images/12-events.png)


<a name="Adding_a_Calendar_Event" />

## <a name="adding-a-calendar-event"></a>Aggiunta di un evento del calendario

Abbiamo visto come leggere i dati del calendario. Ora di seguito viene illustrato come aggiungere un evento a un calendario. Per questa soluzione funzioni, assicurarsi di includere il `android.permission.WRITE_CALENDAR` autorizzazione indicato in precedenza. Per aggiungere un evento in un calendario, è necessario:

1.  Creare un `ContentValues` istanza.
1.  Le chiavi da utilizzare il `CalendarContract.Events.InterfaceConsts` classe per popolare il `ContentValues` istanza.
1.  Impostare il fuso orario per l'inizio dell'evento e di fine.
1.  Utilizzare un `ContentResolver` per inserire i dati dell'evento del calendario.


Il codice riportato di seguito vengono illustrati questi passaggi:

```csharp
ContentValues eventValues = new ContentValues ();

eventValues.Put (CalendarContract.Events.InterfaceConsts.CalendarId,
    _calId);
eventValues.Put (CalendarContract.Events.InterfaceConsts.Title,
    "Test Event from M4A");
eventValues.Put (CalendarContract.Events.InterfaceConsts.Description,
    "This is an event created from Xamarin.Android");
eventValues.Put (CalendarContract.Events.InterfaceConsts.Dtstart,
    GetDateTimeMS (2011, 12, 15, 10, 0));
eventValues.Put (CalendarContract.Events.InterfaceConsts.Dtend,
    GetDateTimeMS (2011, 12, 15, 11, 0));

eventValues.Put(CalendarContract.Events.InterfaceConsts.EventTimezone,
    "UTC");
eventValues.Put(CalendarContract.Events.InterfaceConsts.EventEndTimezone,
    "UTC");

var uri = ContentResolver.Insert (CalendarContract.Events.ContentUri,
    eventValues);
```

Si noti che se non è impostato il fuso orario, un'eccezione di tipo `Java.Lang.IllegalArgumentException` verrà generata. Poiché i valori di ora evento devono essere espresso in millisecondi trascorsi, verrà creata una `GetDateTimeMS` metodo (in `EventListActivity`) per convertire il nostro le specifiche di data in formato millisecondi:

```csharp
long GetDateTimeMS (int yr, int month, int day, int hr, int min)
{
    Calendar c = Calendar.GetInstance (Java.Util.TimeZone.Default);

    c.Set (Java.Util.CalendarField.DayOfMonth, 15);
    c.Set (Java.Util.CalendarField.HourOfDay, hr);
    c.Set (Java.Util.CalendarField.Minute, min);
    c.Set (Java.Util.CalendarField.Month, Calendar.December);
    c.Set (Java.Util.CalendarField.Year, 2011);

    return c.TimeInMillis;
}
```

Se è aggiungere un pulsante per l'elenco di eventi dell'interfaccia utente ed eseguire il codice sopra riportato nella finestra del gestore dell'evento click, l'evento viene aggiunto il calendario e aggiornata in elenco come illustrato di seguito:

[![Schermata dell'app di esempio con gli eventi del calendario, seguita dal pulsante Aggiungi evento di esempio](calendar-images/13.png)](calendar-images/13.png)

Se si apre l'app del calendario, quindi vedremo che l'evento è stata scritta anche:

[![Schermata dell'app del calendario visualizzando l'evento del calendario selezionato](calendar-images/14.png)](calendar-images/14.png)

Come si può notare, Android consente l'accesso rapido e semplice recuperare e rendere persistenti i dati di calendario, consentendo alle applicazioni di integrare funzionalità di calendario.


## <a name="related-links"></a>Collegamenti correlati

- [Demo di calendario (esempio)](https://developer.xamarin.com/samples/CalendarDemo/)
- [Introduzione a Sandwich gelato](http://www.android.com/about/ice-cream-sandwich/)
- [Piattaforma 4.0 Android](http://developer.android.com/sdk/android-4.0.html)
