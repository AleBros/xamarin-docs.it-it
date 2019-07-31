---
title: Uso di CursorAdapter
ms.prod: xamarin
ms.assetid: 60DE467E-A5DA-4420-52E5-D86AD1678FE6
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 10/25/2017
ms.openlocfilehash: ce2f62869057fc83b04b58af37d6ffffd5ad7fb8
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/30/2019
ms.locfileid: "68646481"
---
# <a name="using-cursoradapters-with-xamarinandroid"></a>Uso di CursorAdapter con Novell. Android

Android fornisce classi di adapter specifiche per visualizzare i dati da una query di database SQLite:

 **SimpleCursorAdapter** : simile a un `ArrayAdapter` oggetto perché può essere usato senza una sottoclasse. È sufficiente fornire i parametri obbligatori, ad esempio le informazioni sul cursore e sul layout, nel costruttore e quindi `ListView`assegnare a un oggetto.

 **CursorAdapter** : classe di base da cui è possibile ereditare quando è necessario un maggiore controllo sull'associazione dei valori dei dati ai controlli di layout (ad esempio, nascondere o visualizzare i controlli o modificarne le proprietà).

Gli adattatori di cursore offrono una modalità a prestazioni elevate per scorrere lunghi elenchi di dati archiviati in SQLite. Il codice consumer deve definire una query SQL in un `Cursor` oggetto e quindi descrivere come creare e popolare le visualizzazioni per ogni riga.


## <a name="creating-an-sqlite-database"></a>Creazione di un database SQLite

Per illustrare gli adattatori di cursore è necessaria un'implementazione di database SQLite semplice. Il codice in **SimpleCursorTableAdapter/VegetableDatabase. cs** contiene il codice e SQL per creare una tabella e popolarla con alcuni dati.
La classe `VegetableDatabase` completa è illustrata di seguito:

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

Verrà `VegetableDatabase` creata un'istanza della classe `OnCreate` nel metodo dell' `HomeScreen` attività. La `SQLiteOpenHelper` classe base gestisce la configurazione del file di database e garantisce che SQL nel relativo `OnCreate` metodo venga eseguito una sola volta. Questa classe viene utilizzata nei due esempi seguenti per `SimpleCursorAdapter` e. `CursorAdapter`

Per `_id` ilfunzionamentodellaquerydelcursoreènecessariochesiapresenteunacolonnaditipo`CursorAdapter` Integer. Se la tabella sottostante non dispone di una colonna integer denominata `_id` , utilizzare un alias di colonna per un altro Integer univoco `RawQuery` nell'oggetto che costituisce il cursore. Per ulteriori informazioni, vedere la [documentazione di Android](xref:Android.Widget.CursorAdapter) .


### <a name="creating-the-cursor"></a>Creazione del cursore

Negli esempi viene usato `RawQuery` un oggetto per trasformare una query SQL `Cursor` in un oggetto. L'elenco di colonne restituito dal cursore definisce le colonne di dati disponibili per la visualizzazione nell'adattatore del cursore. Il codice che crea il database nel metodo **SimpleCursorTableAdapter/homescreen. cs** `OnCreate` è illustrato di seguito:

```csharp
vdb = new VegetableDatabase(this);
cursor = vdb.ReadableDatabase.RawQuery("SELECT * FROM vegetables", null); // cursor query
StartManagingCursor(cursor);
// use either SimpleCursorAdapter or CursorAdapter subclass here!
```

Il codice che chiama `StartManagingCursor` deve chiamare `StopManagingCursor`anche. Gli esempi usano `OnCreate` per avviare e `OnDestroy` chiudere il cursore. Il `OnDestroy` metodo contiene il codice seguente:

```csharp
StopManagingCursor(cursor);
cursor.Close();
```

Quando un'applicazione dispone di un database SQLite disponibile e ha creato un oggetto cursore come illustrato, può usare una `SimpleCursorAdapter` sottoclasse o una sottoclasse di `CusorAdapter` per visualizzare le righe `ListView`in un oggetto.


## <a name="using-simplecursoradapter"></a>Uso di SimpleCursorAdapter

`SimpleCursorAdapter`è simile a `ArrayAdapter`, ma specializzato per l'uso con SQLite. Non richiede la creazione di sottoclassi: è sufficiente impostare alcuni parametri semplici quando si crea l'oggetto e quindi assegnarlo `ListView`alla `Adapter` proprietà.

I parametri per il costruttore SimpleCursorAdapter sono i seguenti:

 **Context** : un riferimento all'attività contenitore.

 **Layout** : ID risorsa della visualizzazione riga da usare.

 **ICursor** : cursore che contiene la query SQLite per i dati da visualizzare.

 **Da** matrice di stringhe: una matrice di stringhe corrispondenti ai nomi delle colonne nel cursore.

 **In** una matrice di Integer: matrice di ID di layout che corrispondono ai controlli nel layout di riga. Il valore della colonna specificata nella `from` matrice verrà associato al ControlID specificato in questa matrice nello stesso indice.

Le `from` matrici `to` e devono avere lo stesso numero di voci perché formano un mapping dall'origine dati ai controlli di layout nella visualizzazione.

Il codice di esempio **SimpleCursorTableAdapter/homescreen. cs** collega un `SimpleCursorAdapter` oggetto simile al seguente:

```csharp
// which columns map to which layout controls
string[] fromColumns = new string[] {"name"};
int[] toControlIDs = new int[] {Android.Resource.Id.Text1};
// use a SimpleCursorAdapter
listView.Adapter = new SimpleCursorAdapter (this, Android.Resource.Layout.SimpleListItem1, cursor,
       fromColumns,
       toControlIDs);
```

`SimpleCursorAdapter`è un modo rapido e semplice per visualizzare i dati SQLite in `ListView`un. La limitazione principale è che è possibile associare solo i valori di colonna per visualizzare i controlli, ma non consente di modificare altri aspetti del layout di riga (ad esempio, mostrare/nascondere i controlli o modificare le proprietà).


## <a name="subclassing-cursoradapter"></a>Sottoclasse CursorAdapter

Una `CursorAdapter` sottoclasse presenta gli stessi vantaggi in merito `SimpleCursorAdapter` alle prestazioni di per la visualizzazione dei dati da SQLite, ma offre anche il controllo completo sulla creazione e il layout di ogni visualizzazione riga. L' `CursorAdapter` implementazione è molto diversa dalla creazione di `BaseAdapter` sottoclassi perché non esegue l' `GetView`override `GetItemId`di `Count` , `this[]` o indicizzatore.

Dato un database SQLite funzionante, è sufficiente eseguire l'override di due metodi per creare `CursorAdapter` una sottoclasse:

- **BindView** : data una visualizzazione, aggiornarla per visualizzare i dati nel cursore fornito.

- **NewView** : viene chiamato quando `ListView` richiede una nuova visualizzazione da visualizzare. Si occuperà del riciclo delle visualizzazioni (a differenza del `GetView` metodo sugli adattatori normali). `CursorAdapter`

Le sottoclassi dell'adapter negli esempi precedenti hanno metodi per restituire il numero di righe e per recuperare l'elemento `CursorAdapter` corrente. non richiede questi metodi perché tali informazioni possono essere raccolte dal cursore stesso. Suddividendo la creazione e il popolamento di ogni visualizzazione in questi due metodi, la `CursorAdapter` visualizzazione impone il riutilizzo. Questo è contrario a un adapter normale in cui è possibile ignorare il `convertView` parametro `BaseAdapter.GetView` del metodo.


### <a name="implementing-the-cursoradapter"></a>Implementazione di CursorAdapter

Il codice in **CursorTableAdapter/HomeScreenCursorAdapter. cs** contiene una `CursorAdapter` sottoclasse. Archivia un riferimento al contesto passato nel costruttore `LayoutInflater` `NewView` in modo che possa accedere a un oggetto nel metodo. La classe completa ha un aspetto simile al seguente:

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

In che visualizzerà, creare il cursore e `CursorAdapter` assegnarlo alla visualizzazione elenco. `ListView` `Activity`

Il codice che esegue questa azione nel metodo **CursorTableAdapter/homescreen. cs** `OnCreate` è illustrato di seguito:

```csharp
// create the cursor
vdb = new VegetableDatabase(this);
cursor = vdb.ReadableDatabase.RawQuery("SELECT * FROM vegetables", null);
StartManagingCursor(cursor);

// create the CursorAdapter
listView.Adapter = (IListAdapter)new HomeScreenCursorAdapter(this, cursor, false);
```

Il `OnDestroy` metodo contiene la `StopManagingCursor` chiamata al metodo descritta in precedenza.



## <a name="related-links"></a>Collegamenti correlati

- [SimpleCursorTableAdapter (esempio)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/simplecursortableadapter)
- [CursorTableAdapter (esempio)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/cursortableadapter)
