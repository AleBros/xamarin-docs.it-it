---
title: Uso di CursorAdapter
ms.prod: xamarin
ms.assetid: 60DE467E-A5DA-4420-52E5-D86AD1678FE6
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 10/25/2017
ms.openlocfilehash: 42b9bd528459d8ee941cc293372bf5662a493342
ms.sourcegitcommit: 2eb8961dd7e2a3e06183923adab6e73ecb38a17f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/11/2019
ms.locfileid: "66827621"
---
# <a name="using-cursoradapters"></a>Uso di CursorAdapter


## <a name="overview"></a>Panoramica

Android fornisce le classi di adapter in modo specifico per visualizzare i dati da una query di database SQLite:

 **SimpleCursorAdapter** – Analogamente a un `ArrayAdapter` perché può essere usata senza la creazione di sottoclassi. È sufficiente fornire i parametri obbligatori (ad esempio, un cursore e informazioni sul layout) nel costruttore e quindi assegnare a un `ListView`.

 **CursorAdapter** : i valori di una classe di base che è possibile ereditare da quando è necessario un maggior controllo sull'associazione di dati ai controlli di layout (ad esempio, visualizzare/nascondere i controlli o modificare le relative proprietà).

Gli adattatori di cursore forniscono un modo ad alte prestazioni per lo scorrimento di lunghi elenchi di dati archiviati in SQLite. Il codice consumer deve definire una query SQL in un `Cursor` dell'oggetto e quindi viene descritto come creare e popolare le visualizzazioni per ogni riga.


## <a name="creating-an-sqlite-database"></a>Creazione di un Database SQLite

Per illustrare gli adattatori di cursore richiede una semplice implementazione di database SQLite. Il codice nel **SimpleCursorTableAdapter/VegetableDatabase.cs** contiene il codice e SQL per creare una tabella e popolarla con alcuni dati.
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

Il `VegetableDatabase` sarà possibile creare istanze di classe nel `OnCreate` metodo il `HomeScreen` attività. Il `SQLiteOpenHelper` classe di base gestisce l'installazione del file di database e assicura che il codice SQL nel relativo `OnCreate` metodo verrà eseguito solo una volta. Questa classe viene utilizzata nei due esempi seguenti per `SimpleCursorAdapter` e `CursorAdapter`.

La query del cursore *devono* hanno una colonna integer `_id` per il `CursorAdapter` a funzionare. Se la tabella sottostante non dispone di una colonna di tipo integer denominata `_id` quindi usare un alias di colonna per un altro numero intero univoco nel `RawQuery` che costituisce il cursore. Vedere le [documentazione Android](https://developer.xamarin.com/api/type/Android.Widget.CursorAdapter/) per altre informazioni.


### <a name="creating-the-cursor"></a>Creazione del cursore

Gli esempi usano un `RawQuery` per attivare una query SQL in un `Cursor` oggetto. Elenco di colonne restituito dal cursore definisce le colonne di dati che sono disponibili per la visualizzazione nell'adapter di cursore. Il codice che crea il database di **SimpleCursorTableAdapter/HomeScreen.cs** `OnCreate` metodo è illustrato di seguito:

```csharp
vdb = new VegetableDatabase(this);
cursor = vdb.ReadableDatabase.RawQuery("SELECT * FROM vegetables", null); // cursor query
StartManagingCursor(cursor);
// use either SimpleCursorAdapter or CursorAdapter subclass here!
```

Qualsiasi codice che chiama `StartManagingCursor` deve anche chiamare `StopManagingCursor`. Gli esempi usano `OnCreate` per avviare, e `OnDestroy` per chiudere il cursore. Il `OnDestroy` metodo contiene il codice:

```csharp
StopManagingCursor(cursor);
cursor.Close();
```

Dopo che un'applicazione dispone di un database SQLite disponibile e ha creato un oggetto di cursore, come illustrato, è possibile usare una `SimpleCursorAdapter` o a una sottoclasse `CusorAdapter` per visualizzare le righe in un `ListView`.


## <a name="using-simplecursoradapter"></a>Usando SimpleCursorAdapter

`SimpleCursorAdapter` è simile al `ArrayAdapter`, ma specializzato per l'uso di SQLite. Non richiedono la creazione di sottoclassi: semplicemente impostare alcuni parametri semplice quando si crea l'oggetto e quindi assegnarlo a un `ListView`del `Adapter` proprietà.

I parametri del costruttore SimpleCursorAdapter sono:

 **Contesto** : un riferimento all'attività che lo contiene.

 **Layout** : l'ID risorsa della visualizzazione della riga da utilizzare.

 **ICursor** : un cursore contenente query SQLite per i dati da visualizzare.

 **Da** matrice di stringhe: una matrice di stringhe che corrispondono ai nomi delle colonne del cursore.

 **Per** matrice di interi, una matrice di ID di layout che corrispondono ai controlli nel layout di riga. Il valore della colonna specificata nel `from` matrice verrà associata il ControlID specificati in questa matrice in corrispondenza dell'indice stesso.

Il `from` e `to` matrici deve essere lo stesso numero di voci perché formano un mapping tra l'origine dati e i controlli di layout nella visualizzazione.

Il **SimpleCursorTableAdapter/HomeScreen.cs** cavi del codice di esempio di un `SimpleCursorAdapter` simile al seguente:

```csharp
// which columns map to which layout controls
string[] fromColumns = new string[] {"name"};
int[] toControlIDs = new int[] {Android.Resource.Id.Text1};
// use a SimpleCursorAdapter
listView.Adapter = new SimpleCursorAdapter (this, Android.Resource.Layout.SimpleListItem1, cursor,
       fromColumns,
       toControlIDs);
```

`SimpleCursorAdapter` è un modo semplice e rapido per visualizzare i dati di SQLite in un `ListView`. La limitazione principale è che può essere associato solo i valori di colonna per visualizzare i controlli, non consente di modificare altri elementi del layout di riga (ad esempio, mostrare/nascondere i controlli o modifica delle proprietà).


## <a name="subclassing-cursoradapter"></a>Creazione di una sottoclasse CursorAdapter

Oggetto `CursorAdapter` sottoclasse ha gli stessi vantaggi di prestazioni come il `SimpleCursorAdapter` per visualizzare i dati di SQLite, ma offre anche il controllo completo sulla creazione e layout di ogni vista di riga. Il `CursorAdapter` implementazione è molto diversa dalla creazione di una sottoclasse `BaseAdapter` perché non esegue l'override `GetView`, `GetItemId`, `Count` o `this[]` dell'indicizzatore.

Dato un lavoro database SQLite, è necessario solo eseguire l'override di due metodi per creare un `CursorAdapter` sottoclasse:

- **BindView** – dato una vista, aggiornarlo in modo da visualizzare i dati del cursore specificato.

- **NewView** : viene chiamato quando il `ListView` richiede una nuova vista da visualizzare. Il `CursorAdapter` si occuperà di riciclo viste (a differenza di `GetView` metodo sull'adapter regolari).

Le sottoclassi di adapter negli esempi precedenti hanno metodi per restituire il numero di righe e recuperare l'elemento corrente: il `CursorAdapter` non richiede questi metodi perché tali informazioni è possibile ricavare dalla posizione del cursore stesso. Suddividendo la creazione e la popolazione di ogni vista in questi due metodi, il `CursorAdapter` impone riutilizzo delle visualizzazioni. Ciò avviene in una scheda di regolare in cui è possibile ignorare il `convertView` parametro il `BaseAdapter.GetView` (metodo).


### <a name="implementing-the-cursoradapter"></a>Implementazione di CursorAdapter

Il codice nel **CursorTableAdapter/HomeScreenCursorAdapter.cs** contiene un `CursorAdapter` sottoclasse. Archivia un riferimento di contesto passato al costruttore in modo che possa accedere a un `LayoutInflater` nella `NewView` (metodo). La classe completa è simile alla seguente:

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


### <a name="assigning-the-cursoradapter"></a>Assegnazione di CursorAdapter

Nel `Activity` che verrà visualizzata la `ListView`, crea il cursore e `CursorAdapter` quindi assegnarla alla visualizzazione elenco.

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

- [SimpleCursorTableAdapter (esempio)](https://developer.xamarin.com/samples/monodroid/SimpleCursorTableAdapter/)
- [CursorTableAdapter (esempio)](https://developer.xamarin.com/samples/monodroid/CursorTableAdapter/)
