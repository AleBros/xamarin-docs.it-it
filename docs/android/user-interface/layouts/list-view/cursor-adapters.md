---
title: Utilizzando CursorAdapters
ms.topic: article
ms.prod: xamarin
ms.assetid: 60DE467E-A5DA-4420-52E5-D86AD1678FE6
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 10/25/2017
ms.openlocfilehash: 43d1ef53933ca7867b834dbf118ec730ccbf71ac
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2018
---
# <a name="using-cursoradapters"></a>Utilizzando CursorAdapters

<a name="overview" />

## <a name="overview"></a>Panoramica

Android fornisce le classi di adapter in modo specifico per visualizzare i dati da una query di database SQLite:

 **SimpleCursorAdapter** – Analogamente a un `ArrayAdapter` poiché può essere utilizzata senza la creazione di sottoclassi. Nel costruttore è sufficiente fornire i parametri obbligatori (ad esempio, un cursore e informazioni sul layout) e quindi assegnare un `ListView`.

 **CursorAdapter** : i valori di una classe di base che è possibile ereditare da quando è necessario maggiore controllo sull'associazione di dati ai controlli di layout (ad esempio, visualizzare/nascondere i controlli o modificandone le proprietà).

Schede del cursore consentono di scorrere lunghi elenchi di dati archiviati in SQLite modo ad alte prestazioni. Il codice consumer deve definire una query SQL in un `Cursor` dell'oggetto e quindi viene descritto come creare e popolare le visualizzazioni per ogni riga.

<a name="Creating_an_SQLite_Database" />

## <a name="creating-an-sqlite-database"></a>Creazione di un Database SQLite

Per illustrare le schede di cursore richiede l'implementazione di un database SQLite semplice. Il codice in **SimpleCursorTableAdapter/VegetableDatabase.cs** contiene il codice e SQL per creare una tabella e popolarla con alcuni dati.
L'intero `VegetableDatabase` classe è illustrata di seguito:

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

Il `VegetableDatabase` verrà creata un'istanza delle classe nel `OnCreate` metodo il `HomeScreen` attività. Il `SQLiteOpenHelper` classe di base gestisce l'installazione del file di database e garantisce che l'istruzione SQL nel relativo `OnCreate` metodo viene eseguito solo una volta. Questa classe viene utilizzata nei due esempi seguenti per `SimpleCursorAdapter` e `CursorAdapter`.

La query del cursore *deve* dispone di una colonna integer `_id` per il `CursorAdapter` a funzionare. Se la tabella sottostante non dispone di una colonna di tipo integer denominata `_id` quindi utilizzare un alias di colonna per un altro tipo integer univoco nel `RawQuery` che costituisce il cursore. Consultare la [documenti Android](https://developer.xamarin.com/api/type/Android.Widget.CursorAdapter/) per ulteriori informazioni.

<a name="Creating_the_Cursor" />

### <a name="creating-the-cursor"></a>Creazione del cursore

Negli esempi viene utilizzato un `RawQuery` per attivare una query SQL in un `Cursor` oggetto. Elenco di colonne restituito dal cursore definisce le colonne di dati che sono disponibili per la visualizzazione nella scheda di cursore. Il codice che crea il database di **SimpleCursorTableAdapter/HomeScreen.cs** `OnCreate` metodo è illustrato di seguito:

```csharp
vdb = new VegetableDatabase(this);
cursor = vdb.ReadableDatabase.RawQuery("SELECT * FROM vegetables", null); // cursor query
StartManagingCursor(cursor);
// use either SimpleCursorAdapter or CursorAdapter subclass here!
```

Il codice che chiama `StartManagingCursor` deve anche chiamare `StopManagingCursor`. Gli esempi usano `OnCreate` per avviare, e `OnDestroy` per chiudere il cursore. Il `OnDestroy` metodo contiene il codice:

```csharp
StopManagingCursor(cursor);
cursor.Close();
```

Dopo che un'applicazione dispone di un database SQLite disponibile e ha creato un oggetto di cursore, come illustrato, è possibile utilizzare un `SimpleCursorAdapter` o una sottoclasse di `CusorAdapter` per visualizzare le righe in un `ListView`.

<a name="Using_SimpleCursorAdapter" />

## <a name="using-simplecursoradapter"></a>Utilizzando SimpleCursorAdapter

`SimpleCursorAdapter` è ad esempio il `ArrayAdapter`, ma è specializzato per l'utilizzo con SQLite. Non richiedono la creazione di sottoclassi: quando si crea l'oggetto è sufficiente impostare alcuni parametri semplice e quindi assegnarla a una `ListView`del `Adapter` proprietà.

I parametri per il costruttore SimpleCursorAdapter sono:

 **Contesto** : l'attività che contiene un riferimento.

 **Layout** : l'ID risorsa della visualizzazione riga da utilizzare.

 **ICursor** : un cursore contenente la query SQLite per i dati da visualizzare.

 **Da** matrice di stringhe: una matrice di stringhe corrispondenti ai nomi delle colonne del cursore.

 **Per** matrice di interi: una matrice di ID di layout che corrispondono ai controlli nel layout di riga. Il valore della colonna specificata nel `from` matrice verrà associata il ControlID specificati in questa matrice in corrispondenza dell'indice stesso.

Il `from` e `to` matrici devono avere lo stesso numero di voci perché formano un mapping dell'origine dati per i controlli di layout nella visualizzazione.

Il **SimpleCursorTableAdapter/HomeScreen.cs** cavi codice di esempio di un `SimpleCursorAdapter` come segue:

```csharp
// which columns map to which layout controls
string[] fromColumns = new string[] {"name"};
int[] toControlIDs = new int[] {Android.Resource.Id.Text1};
// use a SimpleCursorAdapter
listView.Adapter = new SimpleCursorAdapter (this, Android.Resource.Layout.SimpleListItem1, cursor,
       fromColumns,
       toControlIDs);
```

`SimpleCursorAdapter` è un modo rapido e semplice per visualizzare i dati di SQLite in un `ListView`. La limitazione principale è che è possibile associare solo i valori di colonna per visualizzare i controlli, quindi non consente di modificare altri aspetti del layout di riga (ad esempio, mostrare/nascondere i controlli o la modifica delle proprietà).

<a name="Subclassing_CursorAdapter" />

## <a name="subclassing-cursoradapter"></a>Creazione di sottoclassi CursorAdapter

A `CursorAdapter` sottoclasse ha gli stessi vantaggi delle prestazioni di `SimpleCursorAdapter` per la visualizzazione dei dati di SQLite, ma offre inoltre il controllo completo sulla creazione e layout di ogni riga di visualizzazione. Il `CursorAdapter` implementazione è molto diversa dalle sottoclassi `BaseAdapter` perché non esegue l'override `GetView`, `GetItemId`, `Count` o `this[]` dell'indicizzatore.

Dato un lavoro SQLite del database, è necessario solo eseguire l'override di due metodi per creare un `CursorAdapter` sottoclasse:

- **BindView** : data una vista, aggiornarlo per visualizzare i dati del cursore specificato.

- **NewView** : viene chiamato quando il `ListView` richiede una nuova vista da visualizzare. Il `CursorAdapter` si occuperà di riciclo viste (a differenza di `GetView` metodo sull'adapter regolari).

Le sottoclassi di adapter negli esempi precedenti sono metodi per restituire il numero di righe e recuperare l'elemento corrente: il `CursorAdapter` non richiede di questi metodi, poiché tali informazioni possono essere potranno dalla posizione del cursore stesso. Suddividendo la creazione e la popolazione di ogni vista in questi due metodi, il `CursorAdapter` Applica visualizzazione utilizzata nuovamente. Si tratta invece di una scheda normale in cui è possibile ignorare il `convertView` parametro del `BaseAdapter.GetView` metodo.

<a name="Implementing_the_CursorAdapter" />

### <a name="implementing-the-cursoradapter"></a>Implementazione di CursorAdapter

Il codice in **CursorTableAdapter/HomeScreenCursorAdapter.cs** contiene un `CursorAdapter` sottoclasse. Archivia un riferimento di contesto passato al costruttore in modo che possa accedere un `LayoutInflater` nel `NewView` metodo. La classe è simile al seguente:

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

<a name="Assigning_the_CursorAdapter" />

### <a name="assigning-the-cursoradapter"></a>L'assegnazione di CursorAdapter

Nel `Activity` che verrà visualizzato il `ListView`, crea il cursore e `CursorAdapter` quindi assegnarlo alla visualizzazione elenco.

Il codice che esegue l'azione nel **CursorTableAdapter/HomeScreen.cs** `OnCreate` metodo è illustrato di seguito:

```csharp
// create the cursor
vdb = new VegetableDatabase(this);
cursor = vdb.ReadableDatabase.RawQuery("SELECT * FROM vegetables", null);
StartManagingCursor(cursor);

// create the CursorAdapter
listView.Adapter = (IListAdapter)new HomeScreenCursorAdapter(this, cursor, false);
```

Il `OnDestroy` metodo contiene il `StopManagingCursor` chiamata al metodo descritto in precedenza.



## <a name="related-links"></a>Collegamenti correlati

- [SimpleCursorTableAdapter (sample)](https://developer.xamarin.com/samples/SimpleCursorTableAdapter/)
- [CursorTableAdapter (esempio)](https://developer.xamarin.com/samples/CursorTableAdapter/)
