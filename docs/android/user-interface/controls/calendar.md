---
title: Calendar
ms.prod: xamarin
ms.assetid: 78666541-CA14-4CD8-A94A-A9621C57813E
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/06/2018
ms.openlocfilehash: d3cba67f27d5b7de7bdcbfa5439061067f759890
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2018
ms.locfileid: "50112533"
---
# <a name="calendar"></a>Calendar


## <a name="calendar-api"></a>Calendario API

Un nuovo set di API introdotte in Android 4 calendario supporta applicazioni che sono progettate per leggere o scrivere dati al provider del calendario. Queste API supportano un'ampia gamma di opzioni di interazione con i dati di calendario, inclusa la possibilità di leggere e scrivere i promemoria, partecipanti ed eventi. Tramite il provider di calendario nell'applicazione, verranno visualizzati i dati che aggiunti tramite l'API nell'app del calendario predefinito fornito con Android 4.


## <a name="adding-permissions"></a>Aggiunta delle autorizzazioni

Quando si usa il nuovo calendario API nell'applicazione, la prima cosa che devi fare è aggiungere le autorizzazioni appropriate per il manifesto Android. Le autorizzazioni è necessario aggiungere sono `android.permisson.READ_CALENDAR` e `android.permission.WRITE_CALENDAR`, a seconda del fatto che si sono la lettura e/o la scrittura dei dati di calendario.


## <a name="using-the-calendar-contract"></a>Utilizzo del contratto del calendario

Dopo aver impostato le autorizzazioni, è possibile interagire con i dati del calendario con il `CalendarContract` classe. Questa classe fornisce un modello di dati che le applicazioni possono usare quando interagiscono con il provider di calendario. Il `CalendarContract` consente alle applicazioni risolvere gli URI per le entità di calendario, ad esempio calendari ed eventi. Fornisce inoltre un modo di interagire con i diversi campi in ogni entità, ad esempio nome e ID, o inizio di un evento e data di fine del calendario.

Esaminiamo un esempio che usa l'API di calendario. In questo esempio verrà esaminato come enumerare i calendari e i relativi eventi, nonché come aggiungere un nuovo evento al calendario.


## <a name="listing-calendars"></a>Elencare i calendari

In primo luogo, si esaminerà come enumerare i calendari che sono stati registrati nell'app del calendario. A tale scopo, è possibile creare un'istanza di un `CursorLoader`. Introdotto in Android 3.0 (API 11), `CursorLoader` è il modo migliore per utilizzare un `ContentProvider`. Come minimo, è necessario specificare il contenuto dell'Uri per i calendari e le colonne che si desidera da restituire. Questa specifica di colonna è noto come un _proiezione_.

La chiamata di `CursorLoader.LoadInBackground` metodo consente di eseguire una query di un provider di contenuti per i dati, ad esempio il provider di calendario.
`LoadInBackground` esegue l'operazione di caricamento effettivo e restituisce un `Cursor` con i risultati della query.

Il `CalendarContract` ci consente di specificare sia il contenuto `Uri` e la proiezione. Per ottenere il contenuto `Uri` per le query calendari, è possibile usare semplicemente il `CalendarContract.Calendars.ContentUri` proprietà simile al seguente:

```csharp
var calendarsUri = CalendarContract.Calendars.ContentUri;
```

Usando il `CalendarContract` per specificare quale calendario colonne che si desidera è altrettanto semplice. È sufficiente aggiungere i campi nel `CalendarContract.Calendars.InterfaceConsts` classe a una matrice. Ad esempio, il codice seguente include l'ID del calendario, il nome visualizzato e nome dell'account:

```csharp
string[] calendarsProjection = {
    CalendarContract.Calendars.InterfaceConsts.Id,
    CalendarContract.Calendars.InterfaceConsts.CalendarDisplayName,
    CalendarContract.Calendars.InterfaceConsts.AccountName
};
```

Il `Id` è importante includere se si usa un `SimpleCursorAdapter` per associare i dati all'interfaccia utente, come vedremo tra breve. Con l'Uri di contenuto e proiezione posto, si crea un'istanza di `CursorLoader` e chiamare il `CursorLoader.LoadInBackground` metodo restituisca un cursore con i dati di calendario, come illustrato di seguito:

```csharp
var loader = new CursorLoader(this, calendarsUri, calendarsProjection, null, null, null);
var cursor = (ICursor)loader.LoadInBackground();

```

L'interfaccia utente per questo esempio contiene un `ListView`, con ogni elemento dell'elenco che rappresenta un singolo calendario. Il codice XML seguente viene illustrato il markup che include il `ListView`:

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

Inoltre, è necessario specificare l'interfaccia utente per ogni elemento dell'elenco, che viene inclusa in un file XML separato, come indicato di seguito:

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

Da questo momento, è sufficiente normale codice Android per associare i dati dalla posizione del cursore nell'interfaccia utente. Si userà un `SimpleCursorAdapter` come indicato di seguito:

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

Nel codice precedente, l'adapter accetta le colonne specificate nella `sourceColumns` della matrice e li scrive in elementi dell'interfaccia utente nel `targetResources` matrice per ogni voce del calendario del cursore. L'attività usata in questo esempio è una sottoclasse di `ListActivity`; include la `ListAdapter` proprietà a cui è impostata l'adapter.

Di seguito è riportata una schermata che mostra il risultato finale con le informazioni di calendario visualizzata nei `ListView`:

[![CalendarDemo in esecuzione nell'emulatore, la visualizzazione di due voci di calendario](calendar-images/11-calendar.png)](calendar-images/11-calendar.png#lightbox)



## <a name="listing-calendar-events"></a>Elenco eventi di calendario

Prossima ora illustrato come enumerare gli eventi per un determinato calendario.
Basandosi su nell'esempio precedente, si verrà presentato un elenco di eventi quando l'utente seleziona uno dei calendari. Pertanto, sarà necessario gestire la selezione di elementi nel codice precedente:

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

In questo codice, stiamo creando un Intent per aprire un'attività di tipo `EventListActivity`, passando l'intento ID del calendario. Saranno necessari l'ID per sapere quale calendario in cui cercare gli eventi. Nel `EventListActivity`del `OnCreate` metodo, è possibile recuperare l'ID di `Intent` come illustrato di seguito:

```csharp
_calId = Intent.GetIntExtra ("calId", -1);
```

A questo punto è possibile gli eventi di query per l'oggetto calendario ID. Il processo per eseguire query per gli eventi è simile al modo in cui viene eseguita una query per un elenco dei calendari in precedenza, solo questa volta si collaborerà con il `CalendarContract.Events` classe. Il codice seguente crea una query per recuperare gli eventi:

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

In questo codice, si ottiene prima del contenuto `Uri` per gli eventi dal `CalendarContract.Events.ContentUri` proprietà. Quindi specifichiamo le colonne di eventi che si desidera recuperare nella matrice eventsProjection.
Infine, si crea un'istanza di un `CursorLoader` con queste informazioni e il caricatore della chiamata `LoadInBackground` metodo restituisca un `Cursor` con i dati dell'evento.

Per visualizzare i dati dell'evento nell'interfaccia utente, è possibile usare markup e codice come abbiamo fatto prima per visualizzare l'elenco dei calendari. Anche in questo caso, usiamo `SimpleCursorAdapter` per associare i dati a un `ListView` come illustrato nel codice seguente:

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

La differenza principale tra il codice e il codice usato in precedenza per visualizzare l'elenco di calendario è l'uso di un `ViewBinder`, che è impostato sulla riga:

```csharp
adapter.ViewBinder = new ViewBinder ();
```

Il `ViewBinder` classe consente di controllare ulteriormente illustrato è associare i valori alle visualizzazioni. In questo caso, abbiamo usarlo per convertire l'ora di inizio evento compreso millisecondi a una stringa di data, come illustrato nell'implementazione seguente:

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

Viene visualizzato un elenco di eventi, come illustrato di seguito:

[![Screenshot dell'app di esempio la visualizzazione di tre eventi del calendario](calendar-images/12-events.png)](calendar-images/12-events.png#lightbox)



## <a name="adding-a-calendar-event"></a>Aggiunta di un evento del calendario

Abbiamo visto come leggere i dati di calendario. Ora vediamo come aggiungere un evento a un calendario. Per questo scopo, assicurarsi di includere il `android.permission.WRITE_CALENDAR` autorizzazione spiegato in precedenza. Per aggiungere un evento a un calendario, si apprenderà come:

1.  Creare un `ContentValues` istanza.
1.  Usare chiavi incluse nel `CalendarContract.Events.InterfaceConsts` classe per popolare il `ContentValues` istanza.
1.  Impostare i fusi orari per l'inizio dell'evento e di fine.
1.  Usare un `ContentResolver` inserirvi i dati dell'evento del calendario.


Il codice seguente illustra questi passaggi:

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

Si noti che se non è impostato il fuso orario, un'eccezione di tipo `Java.Lang.IllegalArgumentException` verrà generata. Poiché i valori di ora evento devono essere espresso in millisecondi trascorsi dal periodo, viene creato un `GetDateTimeMS` metodo (in `EventListActivity`) per convertire la specifiche di data in formato relativo ai millisecondi:

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

Se si aggiungere un pulsante per l'elenco di eventi dell'interfaccia utente ed esegue il codice sopra riportato nel clic del pulsante gestore dell'evento, l'evento viene aggiunto al calendario e aggiornata nel nostro elenco, come illustrato di seguito:

[![Screenshot dell'app di esempio con eventi di calendario seguita dal pulsante di aggiunta evento di campionamento](calendar-images/13.png)](calendar-images/13.png#lightbox)

Se si apre l'app del calendario, quindi vedremo che viene scritto l'evento esiste anche:

[![Screenshot dell'app del calendario visualizzando l'evento del calendario selezionato](calendar-images/14.png)](calendar-images/14.png#lightbox)

Come può notare, Android consente l'accesso rapido e semplice recuperare e rendere persistenti i dati di calendario, consentendo alle applicazioni di integrare facilmente funzionalità di calendario.


## <a name="related-links"></a>Collegamenti correlati

- [Demo di calendario (esempio)](https://developer.xamarin.com/samples/CalendarDemo/)
- [Introduzione a Ice Cream Sandwich](http://www.android.com/about/ice-cream-sandwich/)
- [Piattaforma Android 4.0](http://developer.android.com/sdk/android-4.0.html)
