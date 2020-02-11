---
title: Uso di CursorAdapter
ms.prod: xamarin
ms.assetid: 60DE467E-A5DA-4420-52E5-D86AD1678FE6
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 10/25/2017
ms.openlocfilehash: d0b5845036ab2981a4aa06d2a01ed6b13d094bef
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73028919"
---
# <a name="using-cursoradapters-with-xamarinandroid"></a>Uso di CursorAdapter con Xamarin.Android

Android fornisce classi di adapter specifiche per visualizzare i dati da una query di database SQLite:

 **SimpleCursorAdapter** : simile a un `ArrayAdapter` perché può essere usato senza una sottoclasse. È sufficiente fornire i parametri obbligatori (ad esempio le informazioni sul cursore e sul layout) nel costruttore e quindi assegnare a un `ListView`.

 **CursorAdapter** : classe di base da cui è possibile ereditare quando è necessario un maggiore controllo sull'associazione dei valori dei dati ai controlli di layout (ad esempio, nascondere o visualizzare i controlli o modificarne le proprietà).

Gli adattatori di cursore offrono una modalità a prestazioni elevate per scorrere lunghi elenchi di dati archiviati in SQLite. Il codice consumer deve definire una query SQL in un oggetto `Cursor` e quindi descrivere come creare e popolare le visualizzazioni per ogni riga.

## <a name="creating-an-sqlite-database"></a>Creazione di un database SQLite

Per illustrare gli adattatori di cursore è necessaria un'implementazione di database SQLite semplice. Il codice in **SimpleCursorTableAdapter/VegetableDatabase. cs** contiene il codice e SQL per creare una tabella e popolarla con alcuni dati.
Di seguito è illustrata la classe di `VegetableDatabase` completa:

```csharp
class VegetableDatabase  : SQLiteOpenHelper {
   public static readonly string create_table_sql =
       "CREATE TABLE [vegetables] ([_id] INTEGER PRIMARY KEY AUTOINCREMENT NOT NULL UNIQUE, [name] TEXT NOT NULL UNIQUE)";
   public static readonly string DatabaseName = "vegetables.db";
   public static readonly int DatabaseVersion = 1;
   public VegetableDatabase(Context context) : base(context, DatabaseName, null, DatabaseVersion) { }
   public override void OnCreate(SQLiteDatabase db)
   {
       db.ExecSQL(create_table_sql);
       // seed with data
       db.ExecSQL("INSERT INTO vegetables (name) VALUES ('Vegetables')");
       db.ExecSQL("INSERT INTO vegetables (name) VALUES ('Fruits')");
       db.ExecSQL("INSERT INTO vegetables (name) VALUES ('Flower Buds')");
       db.ExecSQL("INSERT INTO vegetables (name) VALUES ('Legumes')");
       db.ExecSQL("INSERT INTO vegetables (name) VALUES ('Bulbs')");
       db.ExecSQL("INSERT INTO vegetables (name) VALUES ('Tubers')");
   }
   public override void OnUpgrade(SQLiteDatabase db, int oldVersion, int newVersion)
   {   // not required until second version :)
       throw new NotImplementedException();
   }
}
```

Verrà creata un'istanza della classe `VegetableDatabase` nel metodo `OnCreate` dell'attività `HomeScreen`. La classe di base `SQLiteOpenHelper` gestisce la configurazione del file di database e garantisce che SQL nel relativo metodo `OnCreate` venga eseguito una sola volta. Questa classe viene utilizzata nei due esempi seguenti per `SimpleCursorAdapter` e `CursorAdapter`.

Per il corretto funzionamento del `CursorAdapter`, è *necessario* che la query del cursore includa una colonna di tipo Integer `_id`. Se la tabella sottostante non dispone di una colonna di tipo integer denominata `_id` quindi utilizzare un alias di colonna per un altro Integer univoco nell'`RawQuery` che costituisce il cursore. Per ulteriori informazioni, vedere la [documentazione di Android](xref:Android.Widget.CursorAdapter) .

### <a name="creating-the-cursor"></a>Creazione del cursore

Negli esempi viene utilizzato un `RawQuery` per trasformare una query SQL in un oggetto `Cursor`. L'elenco di colonne restituito dal cursore definisce le colonne di dati disponibili per la visualizzazione nell'adattatore del cursore. Di seguito è riportato il codice che crea il database nel metodo di `OnCreate` **SimpleCursorTableAdapter/homescreen. cs** :

```csharp
vdb = new VegetableDatabase(this);
cursor = vdb.ReadableDatabase.RawQuery("SELECT * FROM vegetables", null); // cursor query
StartManagingCursor(cursor);
// use either SimpleCursorAdapter or CursorAdapter subclass here!
```

Il codice che chiama `StartManagingCursor` deve chiamare anche `StopManagingCursor`. Negli esempi viene utilizzato `OnCreate` per avviare e `OnDestroy` per chiudere il cursore. Il metodo `OnDestroy` contiene il codice seguente:

```csharp
StopManagingCursor(cursor);
cursor.Close();
```

Quando un'applicazione dispone di un database SQLite disponibile e ha creato un oggetto cursore come illustrato, può utilizzare una `SimpleCursorAdapter` o una sottoclasse di `CusorAdapter` per visualizzare le righe in un `ListView`.

## <a name="using-simplecursoradapter"></a>Uso di SimpleCursorAdapter

`SimpleCursorAdapter` è simile al `ArrayAdapter`, ma è specializzato per l'uso con SQLite. Non richiede la creazione di sottoclassi: è sufficiente impostare alcuni parametri semplici quando si crea l'oggetto e quindi assegnarlo alla proprietà `Adapter` di un `ListView`.

I parametri per il costruttore SimpleCursorAdapter sono i seguenti:

 **Context** : un riferimento all'attività contenitore.

 **Layout** : ID risorsa della visualizzazione riga da usare.

 **ICursor** : cursore che contiene la query SQLite per i dati da visualizzare.

 **Da** matrice di stringhe: una matrice di stringhe corrispondenti ai nomi delle colonne nel cursore.

 **In** una matrice di Integer: matrice di ID di layout che corrispondono ai controlli nel layout di riga. Il valore della colonna specificata nella matrice `from` verrà associato al ControlID specificato in questa matrice nello stesso indice.

Le matrici `from` e `to` devono avere lo stesso numero di voci perché formano un mapping dall'origine dati ai controlli di layout nella visualizzazione.

Il codice di esempio **SimpleCursorTableAdapter/homescreen. cs** collega un `SimpleCursorAdapter` simile al seguente:

```csharp
// which columns map to which layout controls
string[] fromColumns = new string[] {"name"};
int[] toControlIDs = new int[] {Android.Resource.Id.Text1};
// use a SimpleCursorAdapter
listView.Adapter = new SimpleCursorAdapter (this, Android.Resource.Layout.SimpleListItem1, cursor,
       fromColumns,
       toControlIDs);
```

`SimpleCursorAdapter` è un modo semplice e veloce per visualizzare i dati SQLite in una `ListView`. La limitazione principale è che è possibile associare solo i valori di colonna per visualizzare i controlli, ma non consente di modificare altri aspetti del layout di riga (ad esempio, mostrare/nascondere i controlli o modificare le proprietà).

## <a name="subclassing-cursoradapter"></a>Sottoclasse CursorAdapter

Una sottoclasse `CursorAdapter` ha gli stessi vantaggi in merito alle prestazioni delle `SimpleCursorAdapter` per la visualizzazione dei dati da SQLite, ma offre anche il controllo completo sulla creazione e il layout di ogni visualizzazione riga. L'implementazione del `CursorAdapter` è molto diversa dalla sottoclasse `BaseAdapter` perché non esegue l'override di `GetView`, `GetItemId`, `Count` o `this[]` indicizzatore.

Dato un database SQLite funzionante, è sufficiente eseguire l'override di due metodi per creare una sottoclasse `CursorAdapter`:

- **BindView** : data una visualizzazione, aggiornarla per visualizzare i dati nel cursore fornito.

- **NewView** : viene chiamato quando il `ListView` richiede una nuova visualizzazione da visualizzare. Il `CursorAdapter` si occuperà del riciclo delle visualizzazioni (a differenza del metodo `GetView` sugli adapter normali).

Le sottoclassi dell'adapter negli esempi precedenti hanno metodi per restituire il numero di righe e per recuperare l'elemento corrente, perché il `CursorAdapter` non richiede questi metodi perché tali informazioni possono essere raccolte dal cursore stesso. Suddividendo la creazione e il popolamento di ogni visualizzazione in questi due metodi, il `CursorAdapter` impone il riutilizzo della visualizzazione. Questo si differenzia da un adapter normale in cui è possibile ignorare il parametro `convertView` del metodo `BaseAdapter.GetView`.

### <a name="implementing-the-cursoradapter"></a>Implementazione di CursorAdapter

Il codice in **CursorTableAdapter/HomeScreenCursorAdapter. cs** contiene una sottoclasse `CursorAdapter`. Archivia un riferimento al contesto passato nel costruttore in modo che possa accedere a una `LayoutInflater` nel metodo `NewView`. La classe completa ha un aspetto simile al seguente:

```csharp
public class HomeScreenCursorAdapter : CursorAdapter {
   Activity context;
   public HomeScreenCursorAdapter(Activity context, ICursor c)
       : base(context, c)
   {
       this.context = context;
   }
   public override void BindView(View view, Context context, ICursor cursor)
   {
       var textView = view.FindViewById<TextView>(Android.Resource.Id.Text1);
       textView.Text = cursor.GetString(1); // 'name' is column 1 in the cursor query
   }
   public override View NewView(Context context, ICursor cursor, ViewGroup parent)
   {
       return this.context.LayoutInflater.Inflate(Android.Resource.Layout.SimpleListItem1, parent, false);
   }
}
```

### <a name="assigning-the-cursoradapter"></a>Assegnazione del CursorAdapter

Nella `Activity` in cui verrà visualizzata la `ListView`creare il cursore e `CursorAdapter` quindi assegnarlo alla visualizzazione elenco.

Il codice che esegue questa azione nel `OnCreate` metodo **CursorTableAdapter/homescreen. cs** è illustrato di seguito:

```csharp
// create the cursor
vdb = new VegetableDatabase(this);
cursor = vdb.ReadableDatabase.RawQuery("SELECT * FROM vegetables", null);
StartManagingCursor(cursor);

// create the CursorAdapter
listView.Adapter = (IListAdapter)new HomeScreenCursorAdapter(this, cursor, false);
```

Il metodo `OnDestroy` contiene la chiamata al metodo `StopManagingCursor` descritta in precedenza.

## <a name="related-links"></a>Collegamenti correlati

- [SimpleCursorTableAdapter (esempio)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/simplecursortableadapter)
- [CursorTableAdapter (esempio)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/cursortableadapter)
