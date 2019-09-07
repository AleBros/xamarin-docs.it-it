---
title: Calendario di Novell. Android
ms.prod: xamarin
ms.assetid: 78666541-CA14-4CD8-A94A-A9621C57813E
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/06/2018
ms.openlocfilehash: 63796fc46b126c1e7f99cd1754b58e28f5e36767
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/06/2019
ms.locfileid: "70762421"
---
# <a name="xamarinandroid-calendar"></a>Calendario di Novell. Android

## <a name="calendar-api"></a>API Calendar

Un nuovo set di API di calendario introdotte in Android 4 supporta le applicazioni progettate per la lettura o la scrittura di dati nel provider di calendari. Queste API supportano un'ampia gamma di opzioni di interazione con i dati del calendario, inclusa la possibilità di leggere e scrivere eventi, partecipanti e promemoria. Usando il provider di calendari nell'applicazione, i dati aggiunti tramite l'API verranno visualizzati nell'app Calendar predefinita fornita con Android 4.

## <a name="adding-permissions"></a>Aggiunta di autorizzazioni

Quando si lavora con le nuove API del calendario nell'applicazione, è prima di tutto necessario aggiungere le autorizzazioni appropriate al manifesto Android. Le autorizzazioni che è necessario aggiungere sono `android.permisson.READ_CALENDAR` e `android.permission.WRITE_CALENDAR`, a seconda che si legga e/o si scrivano i dati del calendario.

## <a name="using-the-calendar-contract"></a>Utilizzo del contratto di calendario

Dopo aver impostato le autorizzazioni, è possibile interagire con i dati del calendario utilizzando `CalendarContract` la classe. Questa classe fornisce un modello di dati che le applicazioni possono utilizzare quando interagiscono con il provider del calendario. `CalendarContract` Consente alle applicazioni di risolvere gli URI in entità di calendario, ad esempio calendari ed eventi. Consente inoltre di interagire con diversi campi in ogni entità, ad esempio il nome e l'ID di un calendario, oppure la data di inizio e di fine di un evento.

Viene ora esaminato un esempio che usa l'API Calendar. In questo esempio verrà esaminato come enumerare i calendari e i relativi eventi, nonché come aggiungere un nuovo evento a un calendario.

## <a name="listing-calendars"></a>Elenco di calendari

Esaminare prima di tutto come enumerare i calendari che sono stati registrati nell'app Calendar. A tale scopo, è possibile creare un'istanza `CursorLoader`di. Introdotta in Android 3,0 (API 11) `CursorLoader` , è il modo migliore per utilizzare `ContentProvider`un. Come minimo, è necessario specificare l'URI del contenuto per i calendari e le colonne che si desidera restituire; Questa specifica di colonna è nota come _proiezione_.

La chiamata `CursorLoader.LoadInBackground` al metodo consente di eseguire una query su un provider di contenuti per i dati, ad esempio il provider di calendari.
`LoadInBackground`esegue l'operazione di caricamento effettiva e restituisce `Cursor` un oggetto con i risultati della query.

Consente di specificare sia il contenuto `Uri` che la proiezione. `CalendarContract` Per ottenere il contenuto `Uri` per l'esecuzione di query sui calendari, è possibile `CalendarContract.Calendars.ContentUri` usare semplicemente la proprietà come segue:

```csharp
var calendarsUri = CalendarContract.Calendars.ContentUri;
```

L'utilizzo `CalendarContract` di per specificare le colonne di calendario desiderate è altrettanto semplice. È sufficiente aggiungere campi nella `CalendarContract.Calendars.InterfaceConsts` classe a una matrice. Il codice seguente, ad esempio, include l'ID del calendario, il nome visualizzato e il nome dell'account:

```csharp
string[] calendarsProjection = {
    CalendarContract.Calendars.InterfaceConsts.Id,
    CalendarContract.Calendars.InterfaceConsts.CalendarDisplayName,
    CalendarContract.Calendars.InterfaceConsts.AccountName
};
```

È importante includere se si utilizza un oggetto `SimpleCursorAdapter` per associare i dati all'interfaccia utente, come si vedrà a breve. `Id` Con la proiezione e l'URI del contenuto, viene creata un'istanza `CursorLoader` di e viene `CursorLoader.LoadInBackground` chiamato il metodo per restituire un cursore con i dati del calendario come illustrato di seguito:

```csharp
var loader = new CursorLoader(this, calendarsUri, calendarsProjection, null, null, null);
var cursor = (ICursor)loader.LoadInBackground();

```

L'interfaccia utente per questo esempio contiene `ListView`un oggetto, con ogni elemento dell'elenco che rappresenta un singolo calendario. Nel codice XML seguente viene illustrato il markup che `ListView`include:

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

Inoltre, è necessario specificare l'interfaccia utente per ogni elemento dell'elenco, che viene inserito in un file XML separato, come indicato di seguito:

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

Da questo punto in poi, si tratta semplicemente di un normale codice Android per associare i dati dal cursore all'interfaccia utente. Verrà usato un `SimpleCursorAdapter` come indicato di seguito:

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

Nel codice precedente, l'adapter accetta le colonne specificate nella `sourceColumns` matrice e le scrive negli elementi dell'interfaccia utente `targetResources` nella matrice per ogni voce di calendario del cursore. L'attività utilizzata in questo caso è una `ListActivity`sottoclasse di. `ListAdapter` include la proprietà alla quale viene impostato l'adapter.

Ecco una schermata che mostra il risultato finale, con le informazioni sul calendario visualizzate in `ListView`:

[![CalendarDemo in esecuzione nell'emulatore e visualizzazione di due voci di calendario](calendar-images/11-calendar.png)](calendar-images/11-calendar.png#lightbox)

## <a name="listing-calendar-events"></a>Elenco degli eventi del calendario

Verrà ora esaminato come enumerare gli eventi per un determinato calendario.
Basandosi sull'esempio precedente, verrà presentato un elenco di eventi quando l'utente seleziona uno dei calendari. Sarà quindi necessario gestire la selezione dell'elemento nel codice precedente:

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

In questo codice viene creata una finalità per aprire un'attività di tipo `EventListActivity`, passando l'ID del calendario nello scopo. È necessario l'ID per identificare il calendario in cui eseguire query per gli eventi. Nel metodo `OnCreate`diè possibile recuperare l'ID dall'oggetto `Intent` come illustrato di seguito: `EventListActivity`

```csharp
_calId = Intent.GetIntExtra ("calId", -1);
```

A questo punto è possibile eseguire query sugli eventi per questo ID calendario. Il processo per eseguire una query per gli eventi è simile al modo in cui è stata eseguita una query per ottenere un elenco di calendari in precedenza, `CalendarContract.Events` ma questa volta lavoreremo con la classe. Il codice seguente crea una query per recuperare gli eventi:

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

In questo codice viene prima di tutto ottenuto il `Uri` contenuto per gli eventi `CalendarContract.Events.ContentUri` dalla proprietà. Si specificano quindi le colonne di evento che si desidera recuperare nella matrice eventsProjection.
Infine, viene creata un' `CursorLoader` istanza di con queste informazioni e viene `LoadInBackground` chiamato il metodo del caricatore per `Cursor` restituire un oggetto con i dati dell'evento.

Per visualizzare i dati dell'evento nell'interfaccia utente, è possibile usare markup e codice Analogamente a quanto già fatto in precedenza per visualizzare l'elenco dei calendari. Anche in questo caso `SimpleCursorAdapter` , usiamo per associare i dati `ListView` a un, come illustrato nel codice seguente:

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

La differenza principale tra questo codice e il codice usato in precedenza per visualizzare l'elenco di calendari è l'uso di `ViewBinder`un oggetto, che viene impostato sulla riga:

```csharp
adapter.ViewBinder = new ViewBinder ();
```

La `ViewBinder` classe consente di controllare ulteriormente la modalità di associazione dei valori alle visualizzazioni. In questo caso, viene utilizzato per convertire l'ora di inizio dell'evento da millisecondi a una stringa di data, come illustrato nell'implementazione seguente:

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

Viene visualizzato un elenco di eventi, come mostrato di seguito:

[![Screenshot dell'app di esempio che visualizza tre eventi del calendario](calendar-images/12-events.png)](calendar-images/12-events.png#lightbox)

## <a name="adding-a-calendar-event"></a>Aggiunta di un evento del calendario

Abbiamo visto come leggere i dati del calendario. Si vedrà ora come aggiungere un evento a un calendario. Per eseguire questa operazione, assicurarsi di includere l' `android.permission.WRITE_CALENDAR` autorizzazione citata in precedenza. Per aggiungere un evento a un calendario, è necessario:

1. Creare un' `ContentValues` istanza di.
1. Usare le chiavi della `CalendarContract.Events.InterfaceConsts` classe per popolare l' `ContentValues` istanza.
1. Impostare i fusi orari per le ore di inizio e di fine dell'evento.
1. Utilizzare un `ContentResolver` oggetto per inserire i dati dell'evento nel calendario.

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

Si noti che se non si imposta il fuso orario, verrà generata un'eccezione `Java.Lang.IllegalArgumentException` di tipo. Poiché i valori dell'ora dell'evento devono essere espressi in millisecondi a partire da `GetDateTimeMS` Epoch, viene `EventListActivity`creato un metodo (in) per convertire le specifiche di data in formato millisecondo:

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

Se si aggiunge un pulsante all'interfaccia utente dell'elenco eventi ed è stato eseguito il codice precedente nel gestore dell'evento click del pulsante, l'evento viene aggiunto al calendario e aggiornato nell'elenco, come illustrato di seguito:

[![Screenshot dell'app di esempio con eventi del calendario seguiti dal pulsante Aggiungi evento di esempio](calendar-images/13.png)](calendar-images/13.png#lightbox)

Se si apre l'app Calendar, si noterà che l'evento viene scritto anche qui:

[![Screenshot dell'app Calendar che Visualizza l'evento del calendario selezionato](calendar-images/14.png)](calendar-images/14.png#lightbox)

Come si può notare, Android consente un accesso semplice e potente per recuperare e salvare in modo permanente i dati del calendario, consentendo alle applicazioni di integrare facilmente le funzionalità del calendario.

## <a name="related-links"></a>Collegamenti correlati

- [Demo sul calendario (esempio)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/calendardemo)
- [Introduzione a Ice Cream Sandwich](http://www.android.com/about/ice-cream-sandwich/)
- [Piattaforma Android 4,0](https://developer.android.com/sdk/android-4.0.html)
